---
layout: default
title: VS Code
has_children: false
nav_order: 10
---

## [](#header-1)Connexion par clé SSH
Sur le PC, ouvrir un terminal de commande et saisir la commande suivante pour créer uen clé RSA : 
```
ssh-keygen
```
Saisir un nom pour la clé RSA ou bien valider avec Entrée pour garder le nom par défaut. 
Valider toutes les étapes avec Entrée. 

Afficher la clé publique avec la commande : 
```
type C:\Users\{utilisateur}\.ssh\id_rsa_vscode.pub
```
Recopier le contenu de la clé dans le fichier /home/pi/.ssh/authorized_keys : 
```
nano /home/pi/.ssh/authorized_keys
```
Sur VS Code, appuyer sur F1 et rechercher la commande "Remote-SSH: Open SSH Configuration File"
Sélectionner le fichier par défaut : "C:\Users\\{utilisateur\}\.ssh\config
Pour configurer une nouvelle connexion SSH, ajouter les informations suivantes à la fin du fichier : 
```
Host 192.168.XXX.XXX
  HostName 192.168.XXX.XXX
  User pi
  IdentityFile C:\Users\{utilisateur}\.ssh/id_rsa
  ```
