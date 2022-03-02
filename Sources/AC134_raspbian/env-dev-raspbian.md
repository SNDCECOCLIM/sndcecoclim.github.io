---
layout: default
title: Environnement AC134 RASPBIAN 
parent: Interface AC/RF/HD
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
- **database**                  (Bases de données et fichiers de traduction)
    - **BDD**                   (Fichiers d'extraction de la liste des véhicules depuis Sylob, pour création vstr)
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

## [](#header-2)Compilation
Pour la compilation des fonctions linux ont été créées dans le fichier /home/pi/.bash_aliases (équivalent d'un alias mais un peu plus complexe).

Ces fonctions sont : 
- **mmake** : Compile pour les 2 cartes et crée les fichiers de mise à jour 
- **mmclean**  : Nettoye les ficheirs de compialtion pour les 2 cartes
- **mpackage**  : Copie les fichiers de mise à jour pour les 2 cartes et créé les arfchives de mises à jour.

Les 2 premières fonctions font appel au script [make.sh](https://github.com/SNDCECOCLIM/AC134_RASBIAN/blob/master/make.sh).  
La dernière fait appel au script [package.sh](https://github.com/SNDCECOCLIM/AC134_RASBIAN/blob/master/package.sh).  

## [](#header-2)Release version 
1- Mettre à jour le fichier Historique.log en indiquant les bugs corrigés et nouvelles fonctionnalités apportés par la mise à jour.  

2- Dans le fichier Source/Lib/**affichageVersion.c**, vérifier le numéro de version et passer la version dev à 0 :
```c++
int dev_version = 0; // 1 Si version en cours de développement, 0 si release. // Affiche -DEV derrière l'affichage version si = à 1
int num_version = 5023; // Numéro de version
```
3- Sur Windows, aller dans le dossier V:\BUREAU_ETUDES\CENTRALE AC134\2. CONCEPTION\4. PROGRAMMATION\Base de données. 
Exécuter la macro Excel **Générer_BDD_v2.xls**. Elle va générer la liste des véhicules dans le fichier BDD.csv.  
Copier (Faire glisser BDD depuis windows vers VISUAL SC) ce fichier dans le dossier database/BDD sur la Raspberry Pi de développement.  

4- Sur la Raspberry Pi de développement, se rendre dans le dossier database/BDD et exécuter le script : 
```
cd /home/pi/AC134_RASBIAN/database/BDD
./importvstr
```
Ce script va mettre à jour la base de données vstr dans le dossier database/  

5- Lancer la commande **mpackage** pour inclure la nouvelle base de donnée dans la mise à jour.  Ensuite, lancer la commande **mmake** pour compiler et génerer les **update.tar** et **update_v2.tar** dans AC134_RASBIAN/UPDATE_PACKAGE.

Faire un commit pour indiquer la fin de la nouvelle version. 
```
git add .
git commit -m "Fin version XXXX"
git push 
```
6- Sur Github, créer un pull request de la branche VXXXX vers la branche master. Ceci permet de garder la version Release à jour sur la branche master.  
Verifier les conflits éventuels via l'onglet Pull Request.  
Effectuer le merge une fois les conflits résolus.  

7- Créer la branche pour la nouvelle version. 
```
git checkout -b VXXXX
```
8- Mettre à jour le fichier affichageVersion.c avec la nouvelle version et passer la version dev à 1. 
```c++
int dev_version = 1; // 1 Si version en cours de développement, 0 si release. // Affiche -DEV derrière l'affichage version si = à 1
int num_version = 5024; // Numéro de version
```

9- Faire un commit pour indiquer le début de la nouvelle version : 
```
git add .
git commit -m "Début version XXXX"
git push --set-upstream origin VXXXX
```

10- Archiver l'ancienne mise à jour dans le dossier V:\BUREAU_ETUDES\CENTRALE AC134\2. CONCEPTION\4. PROGRAMMATION\Archives Mises à jour  

11- Copier les 2 fichiers **update.tar** et **update_V2.tar** dans le dossier X:\Mise a jour centrales automatiques_VXXXX.  
Renommer le dossier avec le nouveau numéro de version. 

12- Informer le SAV et/ou les autres services intéressés du déploiement de la nouvelle version. (Eventuellement indiquer les modifications apportées).

13- Ajouter le nouveau numéro de série dans le GDO.   