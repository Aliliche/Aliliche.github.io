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

Pour la phase de développement, j’ai choisi de cross-compiler avec les headers du Kernel. Les headers du Kernel sont un ensemble de fichiers d’entête écrits en C requis pour compiler n’importe quel code pour le Kernel.  En gros c’est une définition des fonctions et des structures qu'on trouve  trouve  dans le kernel source tree, les plus importantsheaders sont dans `include/linux` et `include/asm` mais d’autres aussi  sont ailleurs.

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

### Modules Kernel

{: style="text-align:justify"}
Il y a quelques différences entre la programmation au niveau Kernel et en niveau User Space. 
Un module ou un driver suit les conventions de codage au niveau Kernel.  

Le module définit deux fonctions : `hello_init()` invoquée au chargement et `hello_exit()` invoquée au déchargement du module.  Ces fonctions servent à enregistrer 
le driver au Kernel pour de futurs appelles . La fonction `exit()` doit défaire tout ce que la fonction `init()` a fait, par exemple désallouer de la mémoire. 



`module_init()` et `module exit()` utilisent des macros pour indiquer le rôle de chacune des fonctions "hello" cités précédemment.
`MODULE_LICENCE` pour la licence, sans elle le Kernel va crier et Richard STALLMAN aussi d'ailleur. 

`printk()` se comporte comme `printf()` de la GNU C library.  le Kernel tourne de lui même et ne nécessite pas des librairies C.  
Un programme peut appeler une fonction qu’il n’a pas définit, la  phase de linkage résout ça en utilisant des librairies C, un module par contre n’est linké qu’au Kernel,  donc les seuls fonctions qu’il peut appeler sont celles exportés par ce dérnier.  Exemple:  `printk` est définit au
Kernel et exporté aux modules.   
`KERNEL_ALERT`  définit la priorité du message (info, alterte , erreur ,debug..etc).


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
#### Explication du makefile 

{: style="text-align:justify"}

Normalement pour compiler un module, une simple ligne de code `obj-m := module.o` suffit, le responsable `kbuild system` fera le reste.  

Ce makefile  est lu en deux fois: 

__Quand le makefile est invoqué par une ligne de commande:__  cela signifie que la variable `KERNELRELEASE` est pas définit,
dans ce cas kbuild system  va se charger  de trouver le Kernel source tree.   

__Quand c'est invoqué par Kbuild system:__
Si le kernel pour lequel on compile n’est pas celui de la machine actuelle (eg. notre cas  de  cross compilation)  alors il faut définir  ou se trouve le Kernel source tree,
une fois trouvé, le makefile   appelle la target default et sa commande  va compiler nos modules à partir des fichier objets spécifiés dans  `obj-m`, c’est la seconde lecture (invocation par ligne de commande).  
Dans cette ligne, on change de directory pour aller dans le source tree du kernel pointé avec `-C` la où se trouvele top-level makefile du kernel. La seconde `M=PWD` fait  déplacer ce top-level makefile   sur le dossier courant ou sont 
nos source pour enfin  compiler les fichiers objets specifiés par `obj-m` et générer des fichiers `.ko`

Voici un code qui apelle  le Makefile avec les bons paramétres de cross-compilation:

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
> Pour plus d'informations voir le __chapitre II__ de [Linux Device Drivers 3rd edition]({%  link _posts/2022-02-10-Mes-livres-linux.md %}).  
Pour le makefile, un excellent [Tuto de développez.com](https://gl.developpez.com/tutoriel/outil/makefile/)
{: .prompt-note}


J'envois le module par ssh sur  mon RaspberryPi  et je charge le module et HOP!! la cross-compialtion a fonctionné.
![Loadable Kernel Module](/assets/img/drivers/helloall.png){: w="350" h="450"}
*dmesg*


Pour décharger le module, il faut compiler OpenWRT avec l’option module unload.

Les choses sérieuses commencent ici, il faut discuter d’une chose, le type de notre driver.  

##### Type du driver  

{: style="text-align:justify"}

On peut faire le driver de plusieurs façons, on peut le faire comme un driver de type `char driver`, c’est a dire que notre driver aura une interface de type char dans `sysFs`, on interagit avec le driver via un nœud dans `/dev` et on va initialiser et enregistrer  notre LCD  grâce à des fonctions `init()` et `exit()` au chargement du module. Le driver dans ce cas va utiliser directement les `GPIO` pour envoyer les donnés au  LCD, c'est pas intéressant.  

Une autre façon  serait  d’utiliser le `Bus SPI` et donc l’API SPI du kernel. On va toujours interagir avec le LCD  via `/dev` mais sans utiliser  les fonctions de l’interface char `(file_operations)`. 

En faisant des recherches sur le type d’API, helpers qu’il faut utiliser, je suis tombé sur une API  très intéressants: __Direct Randering Manager__ .  
J’ai trouvé ces helpers DRM car j’ai consulté le driver d’un autre écran LCD TFT que j’ai dans mon stock.  

Par contre, j’en ai trouvé deux types, les helpers DRM et les tiny-drm. Les tiny-drm se veulent plus faciles.  
Perdu , j’ai donc envoyé un message à un des mainteneurs du kernel [Andy Shevchenko](https://github.com/andy-shev), il m’a répondu et a transféré  mon message  pour le mainteneur de la partie DRM du kernel.

Voici sa réponse très intéressante.  

![DRM vs Tiny-drm](/assets/img/drivers/andyshev_message.png){: w="350" h="450"}
*DRM vs Tiny-drm*





Donc décidé, je vais utiliser les helpers DRM. 

D’ailleurs c’est ce qu’on va faire !

##### Device Tree 

{: style="text-align:justify"}
Un device tree est un arbre de structures de  données avec des nœuds décrivants le composant matériel.
Selon ePAPR (power.org)

Le device tree compilé porte une extension `.dtb` __device tree blob__ 
Le fichier avant compilation est un `.dts` __device tree source__.

pour le compiler on utilise le compilateur `dtc` :

``` shell
# installation dtc
sudo apt-get install device-tree-compiler

# Compialtion dts ou dtsi
dtc -I dts -O dtb -o devicetree_file_name.dtb devicetree_file_name.dts

# conversion dts->dtb
dtc -I dts -O dtb -f devicetree_file_name.dts -o devicetree_file_name.dtb

# conversion dtb->dts 
dtc -I dtb -O dts -f devicetree_file_name.dtb -o devicetree_file_name.dts

```
les dtb et dts se trouvent dans   le dossier ou sont décompressés  et compilés les packages : `/build_dir`  .

`bcm2711-rpi-4-b.dtb ` se trouve la ou il y a l'image linux et `bcm2711-rpi-4-b.dts` est dans 
`build_dir/target-aarch64_cortex-a72_musl/linux-bcm27xx_bcm2711/linux-5.15.53/arch/arm/boot/dts`


{: style="text-align:justify"}
le device tree se compose de cette forme :

![Device tree](/assets/img/drivers/dt.png){: w="350" h="450"}
*Device tree.   
Source: Bootlin, Embedded Linux training by Thommas Petazzoni*

Il ya des nœuds qui représentent le device, chaque node est suivis de son adresse dans la mémoire.  

Le node a des propriétés, des  sous node qu'on apelle child nodes et on voit qu’un node peut faire référence à un autre node, on apelle ça un phandle.  
Le node peut aussi avoir un label. 

Pour intégrer le device tree du LCD dans celui du kernel, un certains nombre de propriétés doit exister.  

tout d'abord, il faut que le node décrivant le LCD doit un un "child node" du contrôleur spi, donc toutes les propriétés ce ce controleurs doivent 
être spécifiés.  

voici la section qu'il faut ajouter en dessous du device spi numero 2:

``` c
&spi0 {
	pinctrl-names = "default";
	pinctrl-0 = <&spi0_pins &spi0_cs_pins>;
	cs-gpios = <&gpio 8 1>, <&gpio 7 1>;

	spidev0: spidev@0{
		compatible = "spidev";
		reg = <0>;	/* CE0 */
		#address-cells = <1>;
		#size-cells = <0>;
		spi-max-frequency = <125000000>;
	};

	spidev1: spidev@1{
		compatible = "spidev";
		reg = <1>;	/* CE1 */
		#address-cells = <1>;
		#size-cells = <0>;
		spi-max-frequency = <125000000>;
	};
	5110lcd@3{
		compatible = "adafruit,Nokia5110 LCD";
		reg = <3>;
		spi-max-frequency = <32000000>;
		dc-gpios = <&gpio 9 GPIO_ACTIVE_HIGH>;
		reset-gpios = <&gpio 11 GPIO_ACTIVE_HIGH>;
		backlight = <&bgpio 7 GPIO_ACTIVE_HIGH>;
	};

};
```
### Propriétés
{: style="text-align:justify"}

`compatible <manifacture, modele> `: avec cette propriété , l’OS va décider quel driver va se lier à ce composant.   

`Reg` : Cette propriété dépend du type de bus sur lequel on est raccordé, elle est composé  comme suite `<adresse , length>`,
cela veut dire que la zone réservée pour le node lcd commence à partir de `adresse` et sa taille est `length`. 
Chaque adressse est une liste de __un__ ou __plusieurs__ cellules (cells).  
La taille aussi peut etre une liste de cellules de 32 bits.   

Puisque les adresses sont de variables de taille length , dans le node parent on va specifier:  
`#address-cells` : nombre de cellules 32 bits necessaires pour former l'adresse de reg  
`#size-cells` :  nombre de cellules 32 bits necessaires pour former la taille  de reg   


> il y a deux façons d’ajouter la section dans le DT, soit en cherchant le .dts de la carte cité précédemment, soit en décompilant le dtb, je préfère cette dernière car on a plus d’informations sur la taille des zones mémoires et leurs offsets . 
{: .prompt-note}

Le contrôleur de gpio est spécifié avec 2 cellules `#gpio-cells` donc pour spécifier les gpio,
on besoin d’un phandle sur le contrôleur de gpio, du numéro de la ligne gpio (celle 1) et d’un flag
active low/high (cell 2).  
Il faut regarder le dts décompilé pour voir comment est définit le contrôleur gpio.   

Data/Control gpio : comme dit dans la partie 1 est une entrée  pour choisir le type de donnée selon
ce qu’on lui envoi :  

- 0 ou `GPIO_ACTIVE_HIGH` pour DATA
- 1 ou  `GPIO_ACTIVE_LOW` pour Controle 

Le controleur gpio déclare des pins soit en mode GPIO soit en mode ALT, ce qui veut dire 
alternate function, cela veut dire qu'elle sont utilisé dans un autre mode que le gpio simple
exemple : chip-select du SPI, Clock pour i2c,PWM ...etc
chaque pin peut avoir plusieurs alternate functions

Pour choisir tel ou tel fonction, il faut changer les 3 bits du registre AF .

__En résumé__:  

J'ai choisis dans le device tree un GPIO pour le backlight, un GPIO de RESET et un autre de controle. 
les autres je fais apelle à eux depuis le driver.

### Fonction de probe ()

{: style="text-align:justify"}
Fonction de probe() 

La fonction de probe est appelée  au démarrage du kernel, ou quand on plug le device (dans le cas
d’un device déconnectable). On dit qu’elle est appelée à chaque fois que device est vu __(À compléter  plus tard__ ). 

Son rôle en gros est de détecter le device (dans notre cas le LCD).   
Cette fonction fait l’initialisation du device, l’initialisation du hardware, enregistrer le framework kernel nécessaire ..etc 

Greg-Kroah Hartman disait "les drivers sont simples à écrire, ce qui est difficle est de comprendre le materiel"

Alors !! C'est simple, le chantier est difficle, je dois convetir un driver deja existant qui est un Driver
frambuffer en driver DRM. 
yen a plusieurs mais j'ai choisis détudier le fb_hx8357d.c et son equivalent DRM hx8357d.c 

