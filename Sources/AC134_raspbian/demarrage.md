---
layout: default
title: Drivers & Start
parent: Interface AC/RF/HD
nav_order: 5
---
Au démarrage de la Raspberry les fichiers [S60drivers](https://github.com/SNDCECOCLIM/AC134_RASBIAN/blob/master/installeur/update2/init/S60drivers) et [S70start](https://github.com/SNDCECOCLIM/AC134_RASBIAN/blob/master/installeur/update2/init/S70start) sont éxécutés l'un après l'autre. 
Ces 2 fichiers se trouvent dans le dossier **_/etc/init.d/_**.

# [](#header-1)S60drivers

Le fichier S60drivers est découper en 2 partie.  
La première partie (start) est éxécutée au démarrage de la centrale. Elle configure les différents composants pour lire les capteurs et gérer les relais.  
La deuxième partie (stop) est éxécutée à l'arrêt (arrêt logiciel uniquement). Elle désactive les différents modules qui avaient été chargé au déammarage de la centrale. Voir [Activation des modules au démarrage](https://sndcecoclim.github.io//Sources/AC134_raspbian/modules.html#activation-des-modules-au-d%C3%A9marrage).  

# [](#header-2)Périphériques
`

## [](#header-2)Horloge
L'horloge utiliséee est une [DS3232](../../Datasheets/DS3232.pdf).
Son adresse est 1101000 soit 0x68. Elle ne peut pas être modifiée.  
Pour configurer l'horloge au démarrage, la commande utilisée est : 
```bash
echo ds3232 0x68 > /sys/class/i2c-adapter/i2c-1/new_device
```

## [](#header-2)Balances et Capteurs de pression
Le composant permettant de lire les capteurs de pression est ADC [MCP3423](../../Datasheets/MCP342x.pdf).  
Il possède 2 voies permettant de lire les valeurs du capteur de pression bouteille et nourrice.   
Pour les balances, il s'agit d'un ADC [MCP3424](../../Datasheets/MCP342x.pdf).  
Il possède 4 voies permettant de lire les valeurs de la balance bouteille et des 3 pesons d'huile.  
L'adresse sur le bus I2C de ces deux composants est définie par les pins Adr0 (9) et Adr1 (10).  
![](../../Datasheets/Adresse_MCP342x.png)  ![](../../Datasheets/Tableau_adr_mcp342x.png)  
L'adresse pour les capteurs de pression est 0x6b (1101011).  
L'adresse pour les balances est 0x69 (1101001).  
Pour activer l'ADC au démarrage, il faut exécuter la commande suivante : 
```bash
echo mcp3424 0x69 >/sys/bus/i2c/devices/i2c-1/new_device
echo mcp3423 0x6b >/sys/bus/i2c/devices/i2c-1/new_device
```
La fréquence d'échantillonage de ces ADC peut être programmée. La précision de la mesure varie en fonction de cette fréquence. Le LSB (facteur de conversion) en dépend également.  
La fréquence peut prendre les valeurs suivantes : 

| Fréquence | Précision | LSB       |
|:---------:|:---------:|:---------:|
| 3.75 SPS  | 18 bits   | 15.625 µV |
| 15 SPS    | 16 bits   | 62.5 µV   |
| 60 SPS    | 14 bits   | 250 µV    |
| 240 SPS   | 12 bits   | 1 mV      |

Pour régler ce paramètre, il faut éxécuter les commandes suivantes : 
```bash
echo 3 > /sys/bus/i2c/devices/1-0069/iio\:device0/in_voltage_sampling_frequency
echo 3 > /sys/bus/i2c/devices/1-006b/iio\:device1/in_voltage_sampling_frequency
```
Ici la fréquence d'échantillonage est de 3.75 valeurs par secondes, la précision sera donc de 18 bits et le LSB sera 15.625 µV.  
Pour calculer la tension mesurée, il faut appliquer la formule suivante (le gain PGA est égal à 1):  
![](../../Datasheets/formule_MCP342x.png)

Le facteur LSB est réglé avec les commandes suivantes : 
```bash
echo 0.000015625 > /sys/bus/i2c/devices/1-006b/iio\:device1/in_voltage0_scale
echo 0.000015625 > /sys/bus/i2c/devices/1-0069/iio\:device0/in_voltage0_scale
```
Le gain est réglé avec la valeur in_voltage0_scale (égale à LSB/PGA).  
Dans notre cas, le PGA est toujours égal à 1. 

## [](#header-2)Relais et buzzer
Pour configure le GPIO extender, la commande suivante est éxécutée : 
```bash
echo pca9535 0x21 > /sys/class/i2c-adapter/i2c-1/new_device
```
Elle permet de créer l'arborescence permettant de gérer les nouveaux GPIO dans le dossier /sys/class/gpio/.  

Pour activer les différents GPO en tant qu'entrée ou sortie, le sous-programme **suc** est lancé : 
```bash
/usr/bin/suc
```
Les sources du programme **suc** se trouve dans le dossier [Sources](https://github.com/SNDCECOCLIM/AC134_RASBIAN/tree/master/Sources/app/startup-config).  
Le programme va créer tous les GPIOs nécessaires en envoyant leur numéro à l'adresse /sys/class/gpio/export : 
```bash
echo xxx > /sys/class/gpio/export
```
Pour chaque nouveau GPIO créé, un nouveau dossier **/sys/class/gpioxxx/** est ainsi créé.  
Ce dossier contient les fichiers suivants permettant de configurer le GPIO : 
*   **direction :** Contient **_in_** ou **_out_** pour configurer le GPIO en entrée ou en sortie.  
*   **value :** Fichier dans lequel lire ou écrire l'état du GPIO.  
Le programme **suc** Configure donc chacun des GPIOs contenus dans le fichier [staticio.c](https://github.com/SNDCECOCLIM/AC134_RASBIAN/blob/master/Sources/lib/static_io.c) comme étant des sorties puis les passer à l'état LOW par défaut : 
```C++
    for (i = COMP; i <= BUZZ; i++) {
        gpio_export(i);
        gpio_set_direction(i, 1);
        gpio_write_value(i, 0);
    }
```



# [](#header-1)S70start 

Au démarrage, ce fichier va lancer tout les éxécutables nécessaires au bon fonctionnement de la centrale en tache de fond ().  
* **/sbin/udhcpc & :** Démarrage serveur DHCP
* **/usr/bin/keypadan & :** Gestion du clavier (lit les touches et écrit la touche enfoncée dans /dev/shm/key) 
* **/usr/bin/pressostat & :** Gestion du pressostat (lit l'entrée pressostat et réinitialise la centrale en cas de défaut, permet également le dégazage)
* **watch -n 5 /usr/bin/bouteille > /dev/null 2>&1 & :** Gestion de la pression bouteille (lit la pression dans /dev/shm/psetpoint, en fonction active ou non la chauffe et ecrit l'état de la ceinture chauffante dans /dev/shm/bouteille, s'éxécute toutes les 5s)
* **/usr/bin/rapp & :** Programme de redémarrage quand appui long sur STOP
* **watch -n 1 /usr/bin/buzled > /dev/null 2>&1 & :** Gestion des leds et du buzzer (s'éxécute toutes les secondes)
* **/usr/bin/ac134 & :** Programme principal.
```
watch - n {x} /ma/commande //Permet d'éxécuter une commande toutes les x secondes
```
[Signification > /dev/null 2>&1](https://www.security-helpzone.com/2019/09/22/pourquoi-utiliser-dev-null-2-1/)
