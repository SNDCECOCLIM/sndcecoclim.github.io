---
layout: default
title: Modules
parent: Interface AC/RF/HD
nav_order: 6
---
# [](#header-1)Pourquoi des modules linux? 
Pour pouvoir communiquer avec les différents composants que nous connectons (composants I2C, clavier, écran...), il est nécessaires d'intaller des modules linux (pilotes) sur la Raspberry Pi pour qu'elle sache comment les utiliser.  
[Tutoriel compilation modules Raspberry Pi](https://www.blaess.fr/christophe/2014/03/06/compilation-native-de-modules-kernel-sur-raspberry-pi/) 

Pour les platines non-tactiles, les modules suivants sont nécessaires : 
*   **i2c-dev :** Gestion I2C
*   **i2c-bcm2835 :** Gestion I2C
*   **kpd :** Gestion du clavier
*   **lcd_4_20 :** Gestion de l'écran LCD 4x20
*   **rtc-ds3232 :** Gestion de l'horloge
*   **mcp3422 :** Gestion des ADC MCP3422/3/4
*   **gpio-pca953x :** Gestion des GPIO extenders
*   **lm75 :** Gestion du capteur de température

Pour charger ces modules au démarrage de la Raspberry Pi, il faut les ajouter dans le fichier /etc/modules : 
```
sudo nano /etc/modules
```

Les modules **kpd** et **lcd_4_20** sont des modules spécifiques SNDC.  
Le module **gpio-pca953x** est un module non standard mais dont les sources peuvent être facilement trouvées en ligne.  
Ces trois modules sont compilés spécifiquement pour le projet AC134_RASBIAN.  
Leurs sources sont disponibles dans le dossier [Drivers](https://github.com/SNDCECOCLIM/AC134_RASBIAN/tree/master/drivers).  

Pour pouvoir compiler un module, il faut récupérer les sources du noyau linux de la Raspberry Pi (A minima les headers) . 
Les headers correspondant à notre version de Raspbian se trouve dans le dossier [Drivers](https://github.com/SNDCECOCLIM/AC134_RASBIAN/tree/master/drivers) sous le nom **raspberrypi-kernel-headers_{version des headers}**. La version sauvegardée sur Github correspond aux headers pour le noyau linux version **4.14.98-v7+**.  
Si jamais le noyau a été mis à jour, ou qu'une version plus récente de Raspbian est utilisée, il est possible de retrouver toutes les archives de headers sur le site [archive.raspberrypi.org](https://archive.raspberrypi.org/debian/pool/main/r/raspberrypi-firmware/).  
Une fois le paquet .deb télécharger, il faut le copier sur la Raspberry Pi et installer les headers avec la commande suivante : 

```
sudo dpkg -i /home/pi/raspberrypi-kernel-headers_*.deb
```
Si le noyau linux a été mis à jour à la dernière version, il est également possible d'installer les derniers headers avec les commandes suivantes :  
```
sudo apt update
sudo apt-get install raspberrypi-kernel-headers 
```
Une fois les headers installés, un dossier build doit se trouver dans le dossier **_/lib/modules/4.14.98-v7+_**.  
```
ls /lib/modules/$(uname -r)/
```
Pour connaître le dossier ou seront installés les modules, il faut taper la commande suivante pour récupérer la version du noyau linux : 
```
uname -r
```
Les modules sont installés dans le dossier **/lib/modules/{version noyau}/modules**.  

Pour compiler les modules, copier les fichiers sources dans ce dossier.  
```
cd /lib/modules/$(uname -r)/modules
sudo cp /home/pi/AC134_RASBIAN/drivers/gpio-pca953x/gpio-pca953x.c .
sudo cp /home/pi/AC134_RASBIAN/drivers/kpd/kpd.c .
sudo cp /home/pi/AC134_RASBIAN/drivers/lcd_4_20/lcd_4_20.c .
sudo cp /home/pi/AC134_RASBIAN/drivers/lcd_4_20/lcd_4_20.h .
```
Copier également le fichier Makefile 
```
sudo cp /home/pi/AC134_RASBIAN/drivers/Makefile .
```

Il suffit alors de compiler les modules : 
```
sudo make 
```

Une fois compilés, il faut installer les modules : 
```
sudo insmod kpd.ko
sudo insmod lcd_4_20.ko
sudo insmod gpio-pca953x.ko
```