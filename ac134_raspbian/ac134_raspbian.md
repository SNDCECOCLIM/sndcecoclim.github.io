---
layout: default
title: AC 134 Manuel
has_children: true
nav_order: 1
---
# AC134_RASBIAN

si erreur sqlite 3 : 
```shell
sudo apt-get install libsqlite3-dev
```

# CONFIGURATION SSH VSCode

Sur le pc, ouvrir un invité de commande (cmd dans le menu Démarrer)  
Exécuter la commande suivante :  
```shell
ssh-keygen
```
Valider toutes les étapes avec Entrer  

Afficher la clé ssh publique créée dans le dossier C:\Users\{user}\.ssh\id_rsa.pub
```win32
type C:\Users\{user}\.ssh\id_rsa.pub
```

Copier son contenu dans le fichier /home/pi/.ssh/authorized_keys sur la raspberry  
