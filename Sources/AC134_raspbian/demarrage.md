---
layout: default
title: Drivers & Start
parent: Interface AC/RF/HD
nav_order: 5
---
Au démarrage de la Raspberry les fichiers [S60drivers](https://github.com/SNDCECOCLIM/AC134_RASBIAN/blob/master/installeur/update2/init/S60drivers) et [S70start](https://github.com/SNDCECOCLIM/AC134_RASBIAN/blob/master/installeur/update2/init/S70start) sont éxécutés l'un après l'autre. 
Ces 2 fichiers se trouvent dans le dossier **_/etc/init.d/_**.

# [](#header-1)S60drivers

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
