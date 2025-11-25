---
title: "Reporting Semaine du 03/11/2025"
institute: "DU Capteur et Technologie Innovante - Projet | Fablab | Paris Saclay"
author: "L. Zineddine"
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
# Méthode de mise en œuvre des corrections 
## 1. Remplacement des trois câbles de sortie par un seul câble et installation d’un bloc de distribution
Afin d’améliorer l’organisation et la sécurité du câblage, la première correction consiste à remplacer les trois câbles de sortie actuels par un seul câble d’alimentation principal.
Ce câble unique, contenant la phase, le neutre et la terre, alimentera directement un bloc de distribution (voir image ci-dessous ).
<figure style="text-align: center;">
  <img src="/zineddine/images/1OcF+FgoEL._AC_UF1000,1000_QL80_.jpg"  
  <figcaption> Connecteur de câble électrique </figcaption>
</figure>
Fonctionnement proposé : 
- Le câble principal sort de la serre et alimente en entrée le bloc de distribution.

- Depuis ce bloc, la distribution électrique sera divisée proprement vers les différents grands consommateurs de la serre :

  - La lampe 100 W (éclairage principal de la serre).

  - Le convertisseur AC 240 V → DC 24 V (100 W), utilisé pour alimenter les différents actionneurs ou modules basse tension.

  - L’adaptateur secteur chargé d’alimenter les cartes logiques (Raspberry Pi, Arduino, etc.).
## 2. Amélioration interne au niveau du câblage du bloc 