---
layout: default
title: Toolchain
parent: Interface AC/RF/HD
nav_order: 1
---


https://www.blaess.fr/christophe/2015/12/08/creation-dun-systeme-avec-buildroot-2015-11/

cd /home/pi
mkdir br-tree
cd br-tree
mkdir -p board/rpi/
wget http://www.buildroot.org/downloads/buildroot-2013.08.1.tar.bz2
tar xjf buildroot-2015.11.tar.bz2
cd buildroot-2013.08.1/
make rpi_defconfig
make menuconfig

Voici la liste des modifications apportées :

Menu Target options : pas de changement, mais on peut en profiter pour observer et vérifier le support du processeur cible.
Menu Build options :
Download dir : nous extrayons de l’arborescence de Buildroot ce répertoire dans lequel il stocke les fichiers téléchargés. Ainsi les compilations successives ne nécessiterons pas de nouveaux téléchargements. Nouvelle valeur : $(TOPDIR)/../dl
Host dir : l’emplacement où se trouvera la toolchain compilée. Comme indiqué plus haut, j’ai pour habitude de la placer dans le répertoire board/<target>/cross de notre arborescence de travail. Nouveau chemin : $(TOPDIR)/../board/rpi/cross
Menu System configuration :
Init system : cette option contient au préalable BusyBox mais nous la désactivons pour pouvoir éliminer ce package. Nouvelle valeur : None
Menu Kernel :
Linux Kernel : nous ne voulons, dans un premier temps, produire que la toolchain et rien d’autre. Nous désactivons cette option. Nouvelle valeur : [ ]
Menu Target packages :
BusyBox : c’est le seul package initialement présent. Nous le désactivons. Nouvelle valeur : [ ]
Menu Filesystem images :
tar the root filesystem : inutile, nous ne voulons pas de filesystem pour le moment. Nouvelle valeur : [ ]

cp .config ../board/rpi/buildroot-01.cfg


Lancer une première compilation avec 

sudo make -j4

Lorsque l'erreur suivante apparait : 
Si erreur : 
"Please port gnulib freadahead.c to your platform! Look at the definition of fflush, fread, ungetc on your system, then report this to bug-gnulib."

Appliquer le patch M4-1.4.17_glibc_2.28.patch
Les fichiers se trouvent dans le dossier buildroot-2013.08.1/output/build/host-m4-1.4.16/lib

freadahead.c
fseeko.c
stdio-impl.h

Relancer la compilation avec : 
sudo make -j4

Si l'erreur suivante apparait : 
The directory that should contain system headers does not exist: board/rpi/cross/usr/arm-buildroot-linux-uclibcgnueabi/sysroot/usr/include

bien vérifier les liens dans le dossier Build Options / Download Dir en faisant make menuconfig  (par exemple, ne pas mettre TOP_DIR mais bien TOPDIR)

Si des erreurs liées au dossier /home/pi/br-tree/buildroot-2013.08.1/output/build/host-gcc-final-4.7.3 apparaissent, appliqué le patch 2 dans le dossier suivant : 
 br-tree/buildroot-2013.08.1/output/build/host-gcc-final-4.7.3/gcc/cp (https://gcc.gnu.org/legacy-ml/gcc-patches/2015-08/msg00375.html)

relancer la compilation : 
sudo make -j4

mv /home/pi/br-tree/board/rpi/cross/usr/* /home/pi/AC134_RASBIAN/Toolchain/

Compilation librarie sqlite3

télécharger les sources de sqlite3 dans le dossier STATION_OLD/sqlite3
mkdir -p /home/pi/AC134_RASBIAN/sqlite3/
wget https://www.sqlite.org/2022/sqlite-autoconf-3370200.tar.gz


tar xzf sqlite-autoconf-3360000.tar.gz
rm sqlite-autoconf-3360000.tar.gz
cd sqlite-autoconf-3360000

./configure --host=arm-linux --prefix=/home/pi/AC134_RASBIAN/Toolchain/ CC=/home/pi/AC134_RASBIAN/Toolchain/bin/arm-linux-gcc

make 
make install 

mv sqlite-autoconf-3360000 sqlite

