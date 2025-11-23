---
title: "Reporting Semaine du 03/11/2025"
institute: "DU Capteur et Technologie Innovante - Projet | Fablab | Paris Saclay"
author: "A. Sofiane"
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

## Deploiment GitHub Pages 

La première solution de reporting était basé sur Gitbook proposition payante aprés 14 jours d'essai gratuit, Gitbook offrait une solution "plug and play" avec un certain niveau d'abstraction pour faciliter la rédactions à ses utilisateurs, ne voulant pas s'enfermer dans une proposition payante et au contraire voulant réaliser le maximum de choses par nous-même pour ce projet.

Il était necessaire de déployer notre propre solution de reporting, un framework populaire est l'utilisation de `Mkdocs` et des `action` et Pipeline github basé sur `GithubPages` pour héberger notre propre plateforme de reporting via un pipeline d'intégration continue et developpement continue (CI/CD) nous avons pris le temps de crée une manière robuste et efficace de contribuer au reporting en respectant les bonne pratiques du génie logicielle (Versionnage, CI/CD...), en plus d'établir une convention pour la participation au projet et au reporting trouvable en annexe.

Le [dépot Github](https://github.com/dxhardys/dxhardys.github.io) avec les sources et le pipeline.

Cette documentation s'accompagne d'un guide pour être rapidement opérationnel pour le reporting notamment pour les membres du groupe n'ayant jamais utiliser les outils de versionnage auparant, ce guide est disponible en annexe.

## Suite Modélisation