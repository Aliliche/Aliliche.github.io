---
title: Interface WIFI en Mode Moniteur 
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-05-03 18:13:00 +0100
categories: [Réseaux]
tags: [wifi]
pin: true
---


{: style="text-align:justify" }
J’ai fait une petite modification dans la  partie wireless de Linux,  plus exactement dans l’interface nl80211 qui est une interface de communication entre les programmes du userspace (wpa_supplicant, iw, etc) et des  modules et drivers du kernel cfg80211 et mac80211.  
![Wireless Linux Subsystem](/assets/img/generic/mac80211.png){: w="350" h="250"}
*Wireless Linux Subsystem*
Ma modification portait sur le fait d’autoriser une STA (client) à faire un scan actif, c’est à dire  envoyer des probe request pour découvrir des AP.  

Pour voir ces probe request, on doit configurer une carte radio (celle de mon pc) en mode moniteur. 
Avec le moniteur, on peut écouter tout le trafic sur le canal courant,  on verra des probe request/response, des  beacon frames  mais aussi des paquets de
données et de contrôle.  

On peut faire un moniteur de plusieurs façon, la première  on configurant  notre interface wifi en mode moniteur:  

```console
sudo su 
ip link set wlp2s0 down
iw wlp2s0 set monitor none
ip link set wlp2s0 up 
```


le deuxieme en creant une autre interface wifi virtuelle en mode moniteur  

pour créer une autre interface de type moniteur : 

```console
iw dev wlp2s0 interface add  monit0 type monitor  
ip link set monit0 up 
```
voila ce que donne la commande `iw dev info`  
```console
interface wlp2s0
	ifindex 3
	wdev 0x1
	addr a4:4e:31:cf:dd:a4
	ssid Bbox-D26E0858
	type managed
	channel 48 (5240 MHz), width: 40 MHz, center1: 5230 MHz
	txpower 15.00 dBm
```
J'ai une interface type managed, veut dire mode STA(client) 

> La création d’une autre interface wifi  type moniteur  doit être supporté par le driver
Certains  drivers ne supportent pas deux ifaces fonctionnant en tandem. 
{: .prompt-warning }

#### Petit coup d'oeil sur wireshark 

{: style="text-align:justify" }
Les beacons sont envoyés par les AP chaque 102ms , ils contiennent des informations sur la BSS.  
Un AP envoi un beacon contenant des informations sur lui notamment  son BSSID. Les services qu’il peut fournir, etc.  
Il y a 3 types de trames dans 802.11 :
-  Management frames
-  Data frames
-  Control frames

Un client pour découvrir des AP envoi des probe requests(management frames)  ces derniers peuvent être ciblés vers des AP ou broadcasté. L’AP qui les a reçu  répond par des probe respons.  

Pour voir ces beacons, il faut faire un peux de filtrage, voici les filtres qu'il faut appliquer selons la trame qu'il faut mettre en évidence (0x type sybstype)  
[https://www.wifi-professionals.com/2019/03/wireshark-display-filters](https://www.wifi-professionals.com/2019/03/wireshark-display-filters)  
[version pdf]({% link /assets/pdf/framesfilters.pdf %})

Pour voir les trammes de management, il faut appliquer ce filtre `wlan.fc.type == 0`  

![Management frames](/assets/img/generic/shootwireshark.png){: h="350" w="450"}
*Management frames*

{: style="text-align:justify" }

On voit ma box qui broadcast  son SSID. Mon téléphone qui  envoie et reçoit des trames d’authentification puis envoi  des
Association Request et reçoit Association  Respons  de ma box. 


