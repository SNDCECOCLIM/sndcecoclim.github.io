---
layout: default
title: Environnement AC134 RASPBIAN 
parent: AC 134 Manuel
nav_order: 0
---

# [](#header-1)Mise en place
## [](#header-2)Cloner le projet

Depuis VS Code, se connecter à la Raspberry Pi de développement via l'adresse 192.168.XXX.XXX. 
Touche F1 "Remote-SSH: Connect to host"

Ouvrir le dossier /home/pi

Cloner le projet AC134_RASBIAN : 
```
git clone git@github.com:SNDCECOCLIM/AC134_RASBIAN.git
```
Dans VS Code faire ensuite Fichier -> Ouvrir un nouveau dossier et se placer dans le projet cloné. 

## [](#header-2)Strucuture du projet

- **Build1**                    (Dossier de compilation pour l'ancienne carte) 
- **Build2**                    (Dossier de compilation pour la nouvelle carte)
- **database**                  (Bases de données et ficheirs de traduction)
- **drivers**                   (Sources des pilotes et services pour la nouvelle carte)
    - **gpio-pca953.x**         (Sources pilote GPIO Extender)
    - **kpd**                   (Sources pilote clavier plastron)
    - **lcd_4_20**              (Sources pilote écran LCD)
- **installeur**                (Dossier temporaire pour la création des mises à jour)
- **Sources**
    - **app**                   (main et Makefile des applications)
        - **bouteille**         (Gère la régulation de la chauffe de la bouteille)
        - **buzled**            (Gère le buzzer et les leds)
        - **exerestore**        (Restaure l'éxécutable et gère des mises à jour de BDD)
        - **keypadan**          (Gère le clavier)
        - **pressostat**        (Surveille l'état du pressostat)
        - **rapp**              (Gère le redémarrage par l'appui pendant 3 secondes sur STOP)
        - **reloadapp**         (script pour redémarrer la centrale)
        - **sndc**              (Application principale, renommée en ac134 lors de la création de la mise à jour)
        - **startup-config**    (Configure les GPIO au démarrage)  
    - **lib**                   (Contient tous les fichiers de librairies utilisés par les différentes apps)
- **sqlite3**                   (Sources sqlite3 pour l'ancienne carte)
- **Toolchain**                 (Chaine de cross compilation pour l'ancienne carte :[Toolchain](toolchain.md).)
- **Traduction**                (Scripts de détection des traductions dans le code)
- **UPDATE_PACKAGE**            (Dossier contenant les mises à jours)


