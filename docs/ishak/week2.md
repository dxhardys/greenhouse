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
# Projet de Serre Connectée : Rapport d'Avancement

## 1. Objectifs et Communication Réseau

Le projet vise à établir un système de surveillance et de contrôle à distance pour une serre.

* **Lecture en temps réel :** Acquérir les données de capteurs (température, humidité, CO₂…) dans la serre.
* **Affichage Web :** Afficher ces données sur une page web accessible depuis le réseau local et à distance.
* **Contrôle futur :** Permettre à terme le contrôle des actionneurs (ventilateurs, pompes, éclairage) pour automatiser la serre.

---

## 2. Matériel Utilisé

| Matériel | Description / Modèle | Rôle |
| :--- | :--- | :--- |
| **Raspberry Pi 3 Model B** | 1 Go | Serveur pour collecter les données et héberger la page web. |
| **Arduino Metro Adafruit** | Capteur TMP235 connecté à A2 | Microcontrôleur pour la lecture analogique. |
| **TMP235** | Capteur de température analogique | Lecture de la température. |
| **Câbles USB** | | Liaison Arduino ↔ Raspberry Pi. |
| **Connexion Internet** | 4G/5G | Pour accès distant. |
| **Modem Bbox** | | Pour redirection de port NAT et accès à distance. |

---

## 3. Principe de Fonctionnement

Le système repose sur une communication série entre l'Arduino et le Raspberry Pi, suivant les étapes suivantes :


1.  **Acquisition Arduino :** L'Arduino lit la température du TMP235.
2.  **Génération de données :** L'Arduino génère des données au format **JSON** sur le port série.
3.  **Collecte Raspberry Pi :** Le Raspberry Pi récupère les données via le port USB.
4.  **Hébergement Web :** Un script Python utilisant **Flask** crée un serveur web et affiche les données en temps réel.

### Accessibilité

* Le serveur Flask est accessible sur le réseau local via Wi-Fi.
* L'accès est étendu à distance via la configuration de **Port Forwarding** sur le modem Bbox.

---

## 4. Étapes Réalisées

### 4.1. Lecture du Capteur TMP235

* L’Arduino lit la valeur analogique sur le port **A2**.
* Les valeurs sont converties en tension, puis en température en **°C**.
* Les données sont envoyées au Raspberry Pi via le port série.

Exemple de sortie JSON :
```json
{"temperature": 23.80}

### 4.2. Mise en place du Serveur Web sur Raspberry Pi

-   Installation de **Python 3**, du framework **Flask** et de la librairie **pyserial**.
-   Création d'un script Python pour lire le port série et afficher la température dans une page HTML simple.
-   La page web est configurée pour se rafraîchir toutes les secondes, assurant un affichage en temps réel.

### 4.3. Accès depuis le Réseau Local

-   Accès réussi depuis PC ou téléphone sur le réseau Wi-Fi, après vérification de l'IP locale du Raspberry Pi : `http://192.168.x.x:5000`

### 4.4. Accès à Distance via Port Forwarding

-   Configuration de la Bbox par **redirection du port 5000** vers l'adresse IP locale du Pi.
-   Accès externe réussi depuis un smartphone (4G/5G), en utilisant l'IP publique : `http://IP_PUBLIQUE:5000`

### 4.5. Sécurité et Extensions Futures (Optionnel)

-   Ajout futur de **Cloudflare Tunnel** ou **HTTPS** pour sécuriser l'accès externe.
-   Intégration d’autres capteurs (CO₂) et actionneurs (pompe, ventilateur).
-   Création d’un tableau de bord interactif avec graphiques temps réel.

## 5\. Résultat Obtenu

Le système est opérationnel :

-   Le Raspberry Pi récupère et affiche la température du TMP235 sur une page web.
-   L'accès est garanti localement et à distance via Port Forwarding.
-   Les données brutes sont affichées en JSON et traitées en **°C**.
-   Le système est fonctionnel en temps réel et constitue une base solide pour des extensions.

## 6\. Conclusion

Ce projet démontre la réussite de la mise en réseau d’un microcontrôleur (**Arduino**) et d’un mini-ordinateur (**Raspberry Pi**) pour l'acquisition et le partage de données. Il illustre l'acquisition de données analogiques, leur traitement sur un serveur web et leur consultation à distance, posant les fondations d'une serre connectée et intelligente.