---
title: Driver Linux pour Nokia 5110 LCD (PARTIE 1)
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-04-02 18:47:00 +0100
categories: [Linux, Drivers]
tags: [drivers linux,lcd driver, nokia5010]
pin: true
---

{: style="texti-align:justify"}
Précédent , j’avais parlé d’un [projet de drivers Linux]({% link _posts/2022-02-17-projet-drivers-linux.md %}), on y ait arrivé !  
Le premier driver est donc pour  l’afficheur LCD du Nokia 5010, vous connaissez tous le Nokia 3310 ! le 5110 est son grand père. 

# Le matériel

![5110 Nokia LCD module](/assets/img/drivers/5110.jpg){: w="200" h="350"}
*Module LCD Nokia 5110*

#### Quelques spécifications :

- technologie 	: LCD monochrome 
- résolution 	: 84x48 pixels 
- taille 		: 43,6 mm x 43,1mm 
- Bus 			:  Interface Série 
- contrôleur	:  PCD8544   
- alimentation 	:   2.7V-3.3V

{: style="text-align:justify"}
Cet écran est très utilisé par les bidouilleurs, notamment avec des microcontrôleurs : arduino, pic …
il présente la particularité d’être munie d’un contrôleur  PCD8544 qui contrôle les opérations 
d’affichage.  [voici sa datasheet]({% link /assets/pdf/5110-Nokia.pdf %}), elle nous sera très utile . 


> Mon LCD 5110 est un clone chinois, je ne sais pas si toutes les caractéristiques correspondent à cette datasheet.
{: .prompt-warning }


La cible est  un Raspberry PI 4,  le driver sera cross-compilé sur  une architecture ARM 64-bit. 

![Raspberry Pi 4 ](/assets/img/drivers/pi4.jpg){: w="200" h="350"}
*Raspberry Pi 4 Model B*

 
 J'ai décidé de compiler le driver pour [Automotive Grade Linux](https://www.automotivelinux.org/). Si vous voulez savoir comment compiler AGL pour Rapsberry Pi 4,regardez ici
