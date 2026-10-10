# ESP32 Wireless Pong

## Contexte du projet

Ce projet a été réalisé dans le cadre d’une **SAE** (*Situation d’Apprentissage et d’Évaluation*, **2.03 – Services réseaux et objets communicants*) du **BUT Informatique**, à l’IUT Lyon 1 – site de Bourg-en-Bresse.

La SAÉ est le dispositif pédagogique qui structure les projets du BUT : l’IUT confie aux étudiants un projet conséquent, réalisé en équipe, qui reproduit les conditions d’un travail réel : cahier des charges, gestion de projet, travail collaboratif et livrable évalué. Ces situations d’apprentissage constituent l’un des principaux vecteurs d’évaluation de la formation, en **contrôle continu**.

La SAE 2.03 s’inscrit dans une démarche de conception de services réseaux à travers la création d'un objet communicant matériel. Le projet impose l'utilisation de microcontrôleurs (ESP32-C2), de modules électroniques d'acquisition (analogiques/numériques) et la mise en œuvre d'une communication sans fil directe.

Le projet s’inscrit dans un **cadre pédagogique**, avec un objectif de production concrète associant développement logiciel embarqué et architecture réseau.

---

## Présentation du projet

Dans ce contexte, nous avons développé **ESP32 Wireless Pong**, une **application interactive de jeu de Pong en réseau local sans fil** reliant deux cartes électroniques de manière autonome (sans routeur Wi-Fi).

L’objectif de l’application est de proposer une expérience de jeu d'arcade synchrone et fluide en s'appuyant sur :
*   Une architecture asymétrique Maître / Esclave synchronisée en temps réel.
*   Le protocole bas niveau **ESP-NOW** pour une communication ultra-rapide.
*   Une interface graphique adaptative sur écran OLED embarqué.
*   Des contrôles physiques complets et des retours sonores immersifs.

---

## Objectifs pédagogiques

*   Programmer un microcontrôleur en langage C/C++ (Core Arduino/ESP32).
*   Mettre en œuvre un protocole de liaison de données sans fil (ESP-NOW).
*   Manipuler des bus de communication (I²C) et des entrées/sorties physiques (ADC, PWM, GPIO).
*   Appliquer les concepts de la programmation orientée objet (POO) à un système contraint.
*   Utiliser Git en environnement professionnel pour la maintenance et la gestion des régressions logicielles.

---

## Fonctionnement de l’application

L’application **ESP32 Pong** s’articule autour de plusieurs modes et composants techniques :

### Mode Solo (vs Bot)
*   Affrontement contre une raquette adverse gérée automatiquement (Intelligence Artificielle de suivi de balle).
*   Gestion d'un système à 3 vies, affichage du score en temps réel et sauvegarde d'un état de Pause.

### Mode Multijoueur sans fil
*   Connexion directe via **ESP-NOW**. Le Maître cadence la physique de la balle, intègre la position reçue de l'Esclave, et diffuse l'état complet du monde. 
*   L'Esclave intercepte le paquet et rafraîchit son écran OLED local en transmettant en retour l'état de son joystick.

### Interface & Feedback physique
*   Menu principal navigable de façon fluide à l'aide du joystick (MULTI / SOLO / PARAMÈTRES).
*   Génération d'un signal audio (bip de fréquence) via buzzer piézoélectrique à chaque rebond.
*   Bouton d'interruption matérielle (Bouton C de l'OLED) pour un retour instantané au menu.

---

## Architecture et logique du programme

Le projet applique les principes de la programmation orientée objet (POO) pour séparer strictement la logique métier de la gestion matérielle :
*   `Balle` : Prise en charge des vecteurs physiques de déplacement, accélération progressive et calcul des rebonds complexes.
*   `Barre` : Encapsulation des coordonnées, de la vitesse et des algorithmes de déplacement (Humain ou IA).
*   `Menu` : Machine à états gérant l'arborescence visuelle des écrans graphiques.

Pour éviter la duplication inutile de code (principe DRY), les classes métiers sont isolées dans un dossier partagé commun aux deux nœuds.

---

## Organisation du travail

Projet réalisé en groupe dans le cadre de la SAE. Le suivi des tâches a été managé de façon agile à l'aide d'un diagramme de Gantt.

### Membres du groupe
*   [Yann Madry](https://github.com/yann-madry)
*   [Thomas Bonnefoy](https://github.com/ThomasBonnefoy)

Ma contribution personnelle s’est concentrée principalement sur :
* **La physique et logique du jeu (100% de réalisation) :** Conception intégrale des classes orientées objet `Balle` et `Barre`, du moteur de collisions, et de l'implémentation du cycle des scores et des vies.
* **L'Intelligence Artificielle (90% de réalisation) :** Modélisation et codage de l'algorithme autonome de suivi de balle pour le mode Solo (Bot).
* **L'Interface Utilisateur (66% de réalisation) :** Intégration de la bibliothèque Adafruit, rendu de l'ergonomie visuelle à l'écran et programmation de la logique de navigation des menus.
* **Maintien et gestion Git :** Structuration de l'arborescence partagée pour l'intégration des fonctionnalités et le suivi de version des modules de l'application.

Cette répartition m’a permis de travailler de manière approfondie sur des aspects centrés sur **l'architecture logicielle C++, la robustesse algorithmique et la modularité du code**.

---

## Documents

Les documents associés au projet sont disponibles à la racine du dépôt ou dans le dossier `docs/` :
*   `SAE_203_Sujet.pdf` : Cahier des charges et consignes officielles.
*   `Diagramme_gantt.xlsx` : Suivi de planification prévisionnel et réel de l'équipe.
*   `Fonctionnalites_SAE203.xlsx` : Bilan de l'état d'avancement et de la répartition chiffrée des fonctionnalités.

---

## Implémentation

L’implémentation de l’application a été réalisée en **C/C++** (Core Arduino ESP32 version ≥ 2.0).
Le code est segmenté proprement pour être directement compilé sous Arduino IDE ou PlatformIO :

```text
├── src/
│   ├── common/          # Modules C++ partagés (Classes métiers)
│   │   ├── balle.cpp / .h
│   │   ├── barre.cpp / .h
│   │   └── menu.cpp / .h
│   ├── master/          # Logique d'orchestration Maître
│   │   └── master.ino
│   └── slave/           # Logique du terminal Esclave
│       └── slave.ino
```
---

## Suite du projet

Plusieurs axes d'amélioration ont été identifiés pour parfaire l'application :

* **Optimisation de la fluidité (Mode Multijoueur) :** En mode réseau, le terminal esclave subit des baisses de fluidité visuelle (effet de saccades) liées au rafraîchissement synchrone des paquets. Par manque de temps en fin de projet, ce problème de synchronisation n'a pas pu être résolu.
* **Complexification du gameplay :** Les trajectoires et les angles de rebond de la balle sont actuellement très linéaires et prévisibles. Le jeu mériterait l'ajout d'effets physiques (comme la variation de l'angle de rebond en fonction de la vitesse et du déplacement de la raquette lors de l'impact).
* **Persistance des scores :** Sauvegarde du meilleur score dans la mémoire non-volatile de l'ESP32 afin de conserver les données d'une session à l'autre après extinction des cartes.

Ces éléments constituent des pistes d’évolution et non des limitations bloquantes.
