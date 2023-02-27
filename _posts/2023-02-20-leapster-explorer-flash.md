---
title: Hack de console VTech
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2023-02-20 19:42:00 +0100
categories: [Linux,Hack]
tags: [hack retroleap]
pin: true
---


{: style="text-align: justify" }
Je suis tombé sur un site qui parlait de consoles de jeux pour enfant de 3-11 ans : les consoles VTech.  
VTech est une entreprise  chinoise , un des leaders mondiaux dans la fabrication de jeux et jouets éducatifs électroniques.  



j'en ai acheté une pour en faire quelque chose  
![VTech Leapster explorer](/assets/img/generic/leapster.jpg){: w="250" h="250"}
*Leapster explorer*

{: style="text-align: justify"}
La leapster explorer  est  basée  sur un soc pollux.
Les Soc pollux sont équipés de coeur ARM9 32 bits fonctionnant à 533 MHz.  
Pour  plus de détailles, veuillez consulter ce site: [https://elinux.org/Pollux](https://elinux.org/Pollux)

La Leapster Expolorer fonctionne sous linux, et le but de cette manip et d'installer un firemware dédié à l'émulation : __reatroleap__

retroleap est un firemware de remplacement pour ce genre de console, il intègre notamment  __RetroArch__ pour l’émulation, un rootfs basé sur buildroot..etc.  
RetroArch est un emulateur multiplateforme intégrant plusieurs emulateurs qu’on appelle __core__, il dispose d’une interface en ligne de commande CLI, une GUI et plusieurs outils de configurations.   

![RetroArch](/assets/img/generic/retroarch.png){: w="250" h="250"}
*RetroArch*


Voici le repo git de retroleap : [retroleap](https://github.com/mac2612/retroleap)  
Il suffit de suivre les instructions et c'est tout, c'est simple non ?  bah non 😑.   
Pour une raison ou une autre, la console est détécté par le kernel, on le voit avec `lsub` et `dmesg`, mais refuse de monter un node dans `/dev`,
un des scripte python qui fait apelle à ce node renvoit ` Device not found` et donc l'accès à la console est 
impossible. 

En cherchant sur internet j'ai rien trouvé, donc on passe au plan B.  

## Plan B, se connecter à la console en UART.

La console n'est pas vraiment prévue pour ça, mais en faisant quelque soudures, on peut accéder à la console en série.  
Cela se fait via le port de lecture des cartouches, en effet il y a des sorties Rx et Tx pour le protocole UART. 

![port cartouche: source elinux.org](/assets/img/generic/uart.jpg){: w="250" h="250"}
*port cartouche: soure elinux.org*

Les sorties qu’on voit sur port cartouche sont des sorties UART TTL, pour les rendre utilisables sur le PC on doit utiliser un convertisseur USB ->UART.

