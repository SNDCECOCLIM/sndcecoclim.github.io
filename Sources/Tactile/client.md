---
layout: default
title: Client
parent: Interface Tactile
nav_order: 2
---
## [](#header-2)Simulation client sur PC

Pour simuler le client sur un PC, utiliser la machine virtuelle WSL2 Ubuntu.  
Il est nécessaire le compiler le client sur la VM.  
Il faut d'abord installer les paquets nécessaires à la compilation avec QT :  
```bash
sudo apt install qt5-qmake qt5-default build-essential libqt5websockets5-dev
```
Cloner le projet sndc-client : 
```bash
git clone git@github.com:SNDCECOCLIM/sndc-client.git
```
Compiler le client : 
```bash
qmake 
make -j4
```
Exécuter le client : 
```bash
./sndc-client
```

Pour retirer le message du serveur déconnecter, commenter la ligne suivante au début du fichier [sndcclient.ccp](https://github.com/SNDCECOCLIM/sndc-client/blob/master/src/sndcclient.cpp) :  
```C++
// connect(m_network, SIGNAL(disconnected()), m_window, SLOT(disconnected()));
```