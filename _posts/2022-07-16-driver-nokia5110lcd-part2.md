---
title: Driver Linux pour Nokia 5110 LCD (PARTIE 2)
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-07-15 00:57:00 +0100
categories: [Linux, Drivers]
tags: [drivers linux,lcd driver, nokia5010]
pin: true
---

# Coss-compilation

{: style="text-align:justify"}
Les systèmes Linux embarqués sont assez légers, généralement ils n’embarquent pas de compilateur gcc à l’intérieur, ce qui signifie que les compilations doivent êtres croisées. Sur Openwrt notamment, il n y a pas GDB ni GCC.

Il existe plusieurs façons de cross compiler, le plus simple serait d’intégrer directement notre module dans le système et le compiler avec. C’est d’ailleurs ce que je vais  faire à la fin, créer un module buildroot.  

Pour la phase de développement, j’ai choisi de cross-compiler avec les headers du Kernel. Les headers du Kernel sont un ensemble de fichiers d’entête écrits en C requis pour compiler n’importe quel code pour le Kernel.  En gros c’est une définition des fonctions et des structures.  

Pour compiler notre driver, on doit générer ces headers à partir des sources Kernel de Openwrt qui tourne sur notre RPi4, ensuite on cross-compile sur la target avec la bonne toolchain. J’ai fait cette manipe  car c’est plus simple que de compiler tout le Kernel.  

Ce fichier dessous sert à générer ces Kernel headers:

``` shell
#!/bin/bash

# Ajouter au PATH le repo de la toolchain qui a compilé openwrt
	export PATH=$PATH:~/openwrt/staging_dir/toolchain-aarch64_cortex-a72_gcc-11.3.0_musl/bin/

# Prefix pour la CROSS_COMPILATION
	Prefix=aarch64-openwrt-linux 
# Dossier qui va contenir nos KernelHeaders- 
	KernelHeader=$PWD/KernelHeaders/
# Compile à partir du dossier kernel de Openwrt
	cd ~/openwrt/build_dir/target-aarch64_cortex-a72_musl/linux-bcm27xx_bcm2711/linux-5.15.53
	make ARCH=arm64  CROSS_COMPILE=$Prefix O=$KernelHeader mrproper;
# Default kernel config for the board
	make  ARCH=arm64  CROSS_COMPILE=$Prefix O=$KernelHeader defconfig;
# Preparer les prérequis necessaires pour compiler un out-of-tree module
	make -j 8 ARCH=arm64  CROSS_COMPILE=$Prefix O=$KernelHeader modules_prepare;
```
> Le clean  se fait sur trois niveaux.  
_Make clean_    :	supprimer la plupart des fichiers générés sauf la config et ceux utiles pour contruire des modules  
_make mrproper_ :	supprimer la configuration actuelle et tous les fichiers générés  
_make distclean_:	supprime les fichiers de sauvegarde de l'éditeur, les fichies de patchs , etc.  
{: .prompt-note}

On a maintenant les headers du Kernel, avec ceux ci on doit pouvoir cross-compiler.  
Pour ça il nous faut le petit module kernel : 
``` c
#include <linux/init.h>
#include <linux/module.h>
#include <linux/kernel.h>

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Monkey");
MODULE_DESCRIPTION("A simple Linux Kernel Module");
MODULE_VERSION("0.1");

static int __init hello_init(void)
{
	printk(KERN_ALERT "Hello all\n");
	return 0;
}

static void __exit  hello_exit(void)
{
	printk(KERN_ALERT "Goodbye all\n");
}

module_init(hello_init);
module_exit(hello_exit);
```
Le Makefile de compilation:
```shell
# if KERNELRELEASE is defined, we've been invoked from kernel build system
# and can use its langage
ifneq ($(KERNELRELEASE),)
	obj-m := test.o
# otherwise we were called directly from the command line; invok kbuild system
else
	#safe asignement
	KERNELDIR ?="./KernelHeaders"
	#call out the shell to execute command pwd 
	PWD := $(shell pwd)

.PHONY: modules
modules:
	$(MAKE) -C $(KERNELDIR) M=$(PWD) modules
endif

.PHONY: clean
clean:
	$(MAKE) -C $(KERNELDIR) M=$(PWD) clean

CFLAGS_test.o := -DDEBUG
```
Un code qui apelle  le Makefile avec les bons paramétres de cross-compilation:

```shell
#!/bin/bash

export PATH=$PATH:/home/vicious/openwrt/staging_dir/toolchain-aarch64_cortex-a72_gcc-11.3.0_musl/bin/
# Build the module
	make ARCH=arm64 CROSS_COMPILE=aarch64-openwrt-linux-
# Informations about it
	modinfo test.ko
```
<br>
{: style="text-align:justify"}
> Pour savoir ce qui se traîne dans ce module, il faut voir le __chapitre II__ de [Linux Device Drivers 3rd edition]({%  link _posts/2022-02-10-Mes-livres-linux.md %}).  
Pour le makefile, un excellent [Tuto de développez.com](https://gl.developpez.com/tutoriel/outil/makefile/)
{: .prompt-note}


J'envois le module par ssh sur  mon RaspberryPi  et je charge le module et HOP!! la cross-compialtion a fonctionné.
![Loadable Kernel Module](/assets/img/drivers/helloall.png){: w="350" h="450"}
*dmesg*


Pour décharger le module, il faut compiler OpenWRT avec l’option module unload

{: style="text-align:justify"}
Ok ! Maintenant il faut discuter d’une chose, le type de notre driver.  

On peut faire le driver de plusieurs façons, on peut le faire comme un driver de type `char driver`, c’est a dire que notre driver aura une interface de type char dans `sysFs`, on interagit avec le driver via un nœud dans `/dev` et on va initialiser et enregistrer  notre LCD  grâce à des fonctions `init()` et `exit()` au chargement du module. Le driver dans ce cas va utiliser directement les `GPIO` pour envoyer les donnés au  LCD. Cette façon de faire est simple, et je trouve qu’elle dénature un peux l’aspect des choses.  

L’autre façon de faire et d’utiliser le `Bus SPI` et donc l’API SPI du Kernel. On va toujours interagir avec le LCD  via `/dev` mais sans utiliser  les fonctions l’interface char `(file_operations)`.  

On doit dans ce cas ajouter le LCD dans le `Device Tree` de Linux et écrire une fonction de `probe()` pour détecter.

D’ailleurs c’est ce qu’on va faire !

##### Device Tree 

