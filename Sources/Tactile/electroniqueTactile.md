---
layout: default
title: Carte Electronique Tactile
parent: Interface Tactile
nav_order: 4
---

# [](#header-1)I2C
Installer i2c-tools : 
```
sudo apt install i2c-tools
```

Pour détecter les périphériques I2C, éxécuter : 
```
i2cdetect -y 0
i2cdetect -y 1
```

# [](#header-1)Périphériques
Le fichier gpioConfig.sh permet de configurer les périphériques au démarrage de la Raspberry Pi.  

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
echo 15 > /sys/bus/i2c/devices/1-0069/iio\:device0/in_voltage_sampling_frequency
echo 15 > /sys/bus/i2c/devices/1-006b/iio\:device1/in_voltage_sampling_frequency
```
Ici la fréquence d'échantillonage est de 15 valeurs par secondes, la précision sera donc de 16 bits et le LSB sera 62.5 µV.  
Pour calculer la tension mesurée, il faut appliquer la formule suivante (le gain PGA est égal à 1):  
![](../../Datasheets/formule_MCP342x.png)

Le gain est réglé avec la velur in_voltage_scale (égale à LSB/PGA).  
