---
layout: default
title: Carte Electronique Tactile
parent: Manuel Interface Tactile
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
## [](#header-2)Horloge
L'horloge utiliséee est une DS3232 : [Datasheet DS3232](../../Datasheets/DS3232.pdf).

