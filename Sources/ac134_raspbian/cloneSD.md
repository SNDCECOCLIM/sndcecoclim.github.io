---
layout: default
title: Cloner une carte SD
parent: AC 134 Manuel
nav_order: 3
---



# [](#header-1)Préparer la carte SD en réactivant l'auto expanding
Se connecter en SSH pour effectuer les actions ci-dessous. 
## [](#header-2)Redimensionner la partition
Editer le fichier /boot/cmdline.txt en ajoutant à la fin de la ligne le texte suivant :  
**init=/usr/lib/raspi-config/init_resize.sh**  

Par exemple, le contenu du fichier est initialement : 
```
dwc_otg.lpm_enable=0 console=serial0,115200 console=tty1 root=PARTUUID=1ba1cea3-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait logo.nologo net.ifnames=0

```
et devient donc : 
```
dwc_otg.lpm_enable=0 console=serial0,115200 console=tty1 root=PARTUUID=1ba1cea3-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait logo.nologo net.ifnames=0 init=/usr/lib/raspi-config/init_resize.sh

```
**Attention, le fichier ne doit contenir qu'une ligne, bien vérifier qu'il n'y a pas de retour à la ligne en fin de fichier.**  
## [](#header-2)Redimensionner le système de fichiers 


 [Source](https://blog.dhampir.no/content/shrinking-a-raspbian-installation-and-re-enabling-auto-expanding-for-distribution-of-customized-images).