---
title: "Reporting Semaine du 03/11/2025"
institute: "DU Capteur et Technologie Innovante - Projet | Fablab | Paris Saclay"
author: "B. Ishak"
date: \today
theme: metropolis
colortheme: orchid
fonttheme: structurebold
toc: true
toc-depth: 2
slide-level: 2
header-includes:
  - \metroset{sectionpage=progressbar}
---

# Semaine 3 : Installation et Configuration du Hotspot et du Portail Captif

Ce guide détaille la mise en place d'un système d'accès universel pour la Serre Connectée en utilisant **RaspAP** et un portail captif personnalisé basé sur **NoDogSplash** sur le Raspberry Pi.

## 1\. Préparation du Raspberry Pi

### 1.1 Mise à jour du Système

Mise à jour des listes de paquets et des paquets installés :

Bash

  

sudo apt update
sudo apt upgrade -y

### 1.2 Vérification du Wi-Fi

Assurer que le Wi-Fi est activé et débloqué par le système :

Bash

  

sudo rfkill unblock wifi
iwconfig

## 2\. Installation Complète de RaspAP

RaspAP installe et configure automatiquement les composants nécessaires pour le hotspot et le serveur web : `**hostapd**` (hotspot), `**dnsmasq**` (DHCP), `**lighttpd**` (serveur web) et `**NoDogSplash**` (portail captif).

### 2.1 Installation Officielle de RaspAP

Exécuter le script d'installation :

Bash

  

curl -sL https://install.raspap.com | bash

### 2.2 Redémarrage et Connexion Initiale

Redémarrer le Raspberry Pi pour appliquer les changements :

Bash

  

sudo reboot

#### Connexion au Hotspot par Défaut :

-   **SSID :** `raspi-webgui`
-   **Mot de passe :** `ChangeMe`
-   **Interface web (défaut) :** `http://10.3.141.1`
-   **Identifiants (défaut) :** `admin / secret`

## 3\. Configuration du Hotspot (`hostapd`)

Personnalisation du nom du réseau et des paramètres de sécurité WPA2 dans le fichier `/etc/hostapd/hostapd.conf`.

Bash

  

interface=wlan0
driver=nl80211
ssid=Serre\_Connectee\_AP
hw\_mode=g
channel=6
wmm\_enabled=1
auth\_algs=1
wpa=2
wpa\_passphrase=serre2025
wpa\_key\_mgmt=WPA-PSK
wpa\_pairwise=TKIP
rsn\_pairwise=CCMP

Redémarrer le service `hostapd` pour appliquer les nouveaux paramètres :

Bash

  

sudo systemctl restart hostapd

## 4\. Configuration du Serveur DHCP (`dnsmasq`)

Configuration de la plage d'adresses IP que le Pi distribuera aux clients connectés au hotspot (`wlan0`) dans le fichier `/etc/dnsmasq.conf`.

Bash

  

interface=wlan0
dhcp-range=10.3.141.50,10.3.141.150,255.255.255.0,24h

Redémarrer le service `dnsmasq` :

Bash

  

sudo systemctl restart dnsmasq

## 5\. Activation du NAT (Network Address Translation)

### 5.1 Activer l'IPv4 Forwarding

Pour permettre le routage du trafic d'une interface à l'autre (du Wi-Fi vers Ethernet par exemple), décommenter la ligne dans `/etc/sysctl.conf` :

Bash

  

net.ipv4.ip\_forward=1

Appliquer le changement :

Bash

  

sudo sysctl -p

### 5.2 Règles NAT (Masquerading)

Configuration de la règle NAT pour masquer le trafic sortant via l'interface Ethernet (`eth0`) :

Bash

  

sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo netfilter-persistent save

## 6\. Installation du Portail Captif (`NoDogSplash`)

### 6.1 Installation

Installation du logiciel du portail captif :

Bash

  

sudo apt install nodogsplash -y

### 6.2 Configuration du Portail

Modification des paramètres dans `/etc/nodogsplash/nodogsplash.conf` pour définir l'interface du hotspot et la page de redirection :

Bash

  

GatewayInterface wlan0
GatewayPort 2050
MaxClients 50
GatewayName "Serre Connectee"
AuthIdleTimeout 600
ClientIdleTimeout 600
ForcedRedirectURL http://10.3.141.1/wifi-setup

## 7\. Création de la Page Web du Portail Captif

Le portail redirigera les utilisateurs vers une page de configuration Wi-Fi.

Dossier cible : `/etc/nodogsplash/htdocs/`

**Créer le fichier** `wifi-setup.html` :

HTML

  

<html> 
<head><title>Configuration Wi-Fi</title></head> 
<body> 
    <h2>Configuration du réseau Wi-Fi</h2> 
    <form action="/cgi-bin/connect" method="post"> 
        Nom du réseau (SSID) : <input name="ssid"><br> 
        Mot de passe : <input name="password" type="password"><br> 
        <input type="submit" value="Se connecter"> 
    </form> 
</body> 
</html>

## 8\. Script CGI Permettant la Connexion Wi-Fi

Le script CGI gère les données soumises par le formulaire pour modifier la configuration Wi-Fi du Pi.

### 8.1 Activer CGI pour Lighttpd

Installer et activer le support CGI pour le serveur web `lighttpd` :

Bash

  

sudo apt install lighttpd lighttpd-mod-fastcgi -y
sudo lighttpd-enable-mod fastcgi
sudo service lighttpd restart

### 8.2 Script de Connexion

Créer le fichier exécutable `/usr/lib/cgi-bin/connect` avec le contenu suivant :

Bash

  

#!/bin/bash
echo "Content-type: text/html"
echo ""

read input

ssid=$(echo "$input" | sed -n 's/.\*ssid=\\(\[^&\]\*\\).\*/\\1/p' | sed 's/+/ /g' | sed 's/%/\\\\x/g' | xargs -0 printf %b)
password=$(echo "$input" | sed -n 's/.\*password=\\(\[^&\]\*\\).\*/\\1/p' | sed 's/+/ /g' | sed 's/%/\\\\x/g' | xargs -0 printf %b)

# Réécriture du fichier wpa\_supplicant.conf
cat <<EOF > /etc/wpa\_supplicant/wpa\_supplicant.conf
network={
    ssid="$ssid"
    psk="$password"
}
EOF

# Tenter la reconnexion
wpa\_cli -i wlan0 reconfigure

echo "<html><body>Connexion au réseau $ssid...</body></html>"

Rendre le script exécutable :

Bash

  

sudo chmod +x /usr/lib/cgi-bin/connect

## 9\. Redémarrage des Services

Redémarrer tous les services clés pour s'assurer que la nouvelle configuration est bien prise en compte :

Bash

  

sudo systemctl restart hostapd
sudo systemctl restart dnsmasq
sudo systemctl restart nodogsplash
sudo systemctl restart lighttpd

## 10\. Flux Complet de Connexion

Ce flux résume l'expérience utilisateur finale :

1.  L’utilisateur se connecte au réseau `**Serre_Connectee_AP**`.
2.  Le portail captif `**NoDogSplash**` intercepte la première requête HTTP.
3.  La page `**wifi-setup.html**` s’affiche automatiquement.
4.  L’utilisateur choisit le réseau Wi-Fi de la maison et entre le mot de passe.
5.  Le script CGI met à jour le fichier `**wpa_supplicant.conf**`.
6.  Le Raspberry Pi se connecte automatiquement au réseau Wi-Fi de la maison.
7.  Le serveur Flask devient accessible en local (via le routeur) ou à distance (via port forwarding).

### Résultat Final

-   Hotspot stable et opérationnel.
-   Portail captif fonctionnel sans redirection externe.
-   Configuration du Wi-Fi sans écran ni clavier.
-   Connexion Internet automatique.
-   Accès aux données en temps réel de la serre connecté