---
title: Build Yocto sur Raspberry-Pi 4 [Act 2]
author:
name: Aliliche larbi
link: https://github.com/Aliliche
date: 2026-03-22 21:49:00 +0100
categories: [Yocto, ]
pin: true
---

# Dépots et clonage

Des layers sont necessaires pour le build de l'image rpi4

```bash
git clone -b dunfell git://git.openembedded.org/meta-openembedded

git clone -b dunfell git://git.yoctoproject.org/meta-raspberrypi

## faire des pull régulierement
```

La bonne pratique consiste à les cloner a coté du dossier poky.


# Images

## Le dossier `meta` dans Yocto

Dans Yocto, le dossier `meta` contient les **métadonnées** essentielles pour construire une image Linux. C’est l’endroit où l’on définit comment compiler, configurer et assembler le système.

## Contenu typique

1. **`recipes-*`**
   - Contient des **recettes BitBake** pour les paquets.
   - Chaque recette décrit comment télécharger, compiler et installer un logiciel.
   - Exemples :
     - `recipes-core`
     - `recipes-kernel`
     - `recipes-graphics`

2. **`conf`**
   - Contient des fichiers de configuration pour BitBake et Yocto.
   - Exemples :
     - `layer.conf` : définit le nom du layer, la compatibilité, les priorités.
     - `machine/*.conf` : configuration spécifique pour une machine/cible.

3. **`classes`**
   - Contient des **classes BitBake** (`.bbclass`) pour réutiliser des fonctions entre recettes.
   - Exemple : `kernel.bbclass` pour compiler des kernels.

4. **`files` ou `scripts`**
   - Contient des fichiers supplémentaires utilisés par les recettes.
   - Exemples : scripts de patch, fichiers de configuration, templates.

5. **`docs` (optionnel)**
   - Documentation du layer.


## Images poky

Les recettes images minimales sont dans /recipes-core: 
``` bash 
build-appliance-image            core-image-base.bb     core-image-minimal-dev.bb        core-image-minimal-mtdutils.bb
build-appliance-image_15.0.0.bb  core-image-minimal.bb  core-image-minimal-initramfs.bb  core-image-tiny-initramfs.bb
```

Toutes ces images  heritent une image mére:

```
meta/classes/core-image.bbclass

```
Certaines images requirent aussi d'autres image. voir "require"

Certaines recettes ajoutent : 
```
MAGE_FEATURES += "dev-pkgs"

```
Se sont des features qu'on trouve dans la classe mere core-image.bbclass, un serveur x11, ssh ..etc

## Images Raspberry-PI

```
```
Les images pour le Raspberry-Pi sont  dans la layer clonnée meta-raspberrypi/recipes-core/images

```
rpi-basic-image.bb  rpi-hwup-image.bb  rpi-test-image.bb
```
L'image qu'on va compiler pour le Raspberry est rpi-test-image. 

```
# Base this image on core-image-base
include recipes-core/images/core-image-base.bb ### Inclue l'image de base poky

COMPATIBLE_MACHINE = "^rpi$"

IMAGE_INSTALL_append = " packagegroup-rpi-test" ## inclue dans le rootfs un package groupe de test

```

- IMAGE_INSTALL_append
    - C’est une extension de la variable IMAGE_INSTALL
    - Elle ajoute du contenu sans écraser ce qui existe déjà


Le paquet packagegroup-rpi-test est une recette qui installe plusieurs paquets d’un coup

Au lieu de faire :

```
IMAGE_INSTALL += "htop i2c-tools stress-ng iperf3"

```
On fait :

```
IMAGE_INSTALL += "packagegroup-rpi-test"
```

la recette packagegroup est dans recipes-core.

```
RDEPENDS_${PN} = "..."

```
C’est là que sont listés les vrais paquets installés.



# Compilation de l'image rpi-test-image.

```
source poky/oe-init-build-env build-rpi4

bitbake-layers add-layer ../meta-openembedded/meta-oe ## l'odre est imprtant

bitbake-layers add-layer ../meta-openembedded/meta-python

..
..
```
Il faut ajouter toutes ces layers:

```
vicious@debian:~/git/learn_yocto/build-rpi4/conf$ bitbake-layers show-layers
NOTE: Starting bitbake server...
layer                 path                                      priority
==========================================================================
meta                  /home/vicious/git/learn_yocto/poky/meta   5
meta-poky             /home/vicious/git/learn_yocto/poky/meta-poky  5
meta-yocto-bsp        /home/vicious/git/learn_yocto/poky/meta-yocto-bsp  5
meta-oe               /home/vicious/git/learn_yocto/meta-openembedded/meta-oe  6
meta-python           /home/vicious/git/learn_yocto/meta-openembedded/meta-python  7
meta-networking       /home/vicious/git/learn_yocto/meta-openembedded/meta-networking  5
meta-multimedia       /home/vicious/git/learn_yocto/meta-openembedded/meta-multimedia  6
meta-raspberrypi      /home/vicious/git/learn_yocto/meta-raspberrypi  9

```
La commande add-layer écrit cette variable dans le fichier bblayers.conf
```
BBLAYERS ?= " \
  /home/vicious/git/learn_yocto/poky/meta \
  /home/vicious/git/learn_yocto/poky/meta-poky \
  /home/vicious/git/learn_yocto/poky/meta-yocto-bsp \
  /home/vicious/git/learn_yocto/meta-openembedded/meta-oe \
  /home/vicious/git/learn_yocto/meta-openembedded/meta-python \
  /home/vicious/git/learn_yocto/meta-openembedded/meta-networking \
  /home/vicious/git/learn_yocto/meta-openembedded/meta-multimedia \
  /home/vicious/git/learn_yocto/meta-raspberrypi \
```


```
icious@debian:~/git/learn_yocto/meta-raspberrypi/conf/machine$ ls
include            raspberrypi0-wifi.conf  raspberrypi3-64.conf  raspberrypi4-64.conf  raspberrypi-cm3.conf  raspberrypi.conf
raspberrypi0.conf  raspberrypi2.conf       raspberrypi3.conf     raspberrypi4.conf     raspberrypi-cm.conf
```

dans local.conf, ajouter la machine et un serveur ssh.

```
MACHINE = "raspberrypi4-64"
EXTRA_IMAGE_FEATURES ?= "debug-tweaks ssh-server-openssh"

```
Enfin
```
bitbake rpi-test-image
```

L'image est dans tmp/deploy/images/raspberrypi4-64:
```
lrwxrwxrwx 2 vicious vicious        60 Mar 20 22:13 rpi-test-image-raspberrypi4-64.wic.bz2 -> rpi-test-image-raspberrypi4-64-20260320190008.rootfs.wic.bz2
```
- .wic.bz2 → bootable SD card
    - Image disque complète (boot + rootfs)
Contient :
    - partition boot (FAT)
    - kernel + dtb
    - rootfs

Flash sur sdcard :

```
bunzip2 rpi-test-image-raspberrypi4-64.wic.bz2
sudo dd if=rpi-test-image-raspberrypi4-64.wic of=/dev/sdX bs=4M status=progress 
sync

```




