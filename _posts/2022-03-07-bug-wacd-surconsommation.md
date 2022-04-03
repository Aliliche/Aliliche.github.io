---
title: Fuites mémoire, les dangers des tableaux en C
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-03-06 14:59:00 +0100
categories: [Bugs,Openwrt]
tags: [fuites mémoire, Openwrt]
mermaid: true
pin: true
---

Petit bug intéressant ! 

Je travaillais sur un daemon  custom écrit en C qui s’appelle WACD. Ce daemon produisait une surconsommation  de  la RAM telle ! que le système IPC (ubus)  dont il est inclut crachait.

La cause de cette surconsommation est... un simple  `unint8_t`   
regardez bien ce petit code :

```c
extern ap_info_t *ap_track_add(prob_announce_t prob)
{
	struct ap_info *ap;
	local_ap_info_t *local_ap;
	//uint8_t hash_index = prob.ap_macaddr[5];
	int i;
	
	uint8_t ap_index = -1;

/* ap = ap_track_get(prob.ap_macaddr, prob.ssid);
	if (ap) { */
		/* Move the most recent entry to the end of the list */
	/*  list_del_init(&ap->list);
		list_add_tail(&ap->list, &ap_seen[hash_index]);
		time(&ap->last_seen);
		ap->num_sta_associated = prob.num_sta_associated;
		ap->band_capabilities = prob.band_capabilities;
		memcpy(ap->ssid, prob.ssid, SSID_MAX_LEN);
		ap->max_sta_num = prob.max_sta_num;
		return ap;
	} */

	 Add a new entry 
	// Get last ap_index
	for (i = 0; i < MAX_LOCAL_AP; i++) {
		if(ap_list[i] == NULL) {
			ap_index = i;
			break;
		}
	}
	if(ap_index == -1) {
		dbg_print(LOG_ERR, "AP list is full.\n");
		return NULL;
	}
	local_ap = malloc(sizeof(local_ap_info_t));
	if (!local_ap) {
		dbg_print(LOG_ERR, "Cannot allocate memory for local_ap.\n");
		return NULL;
	}
	local_ap->gctx = NULL;
	memset(&local_ap->ap, 0, sizeof(ap_info_t));
	ap = &local_ap->ap;
	memcpy(ap->macaddr, prob.ap_macaddr, ETH_ALEN);
	memcpy(ap->ssid, prob.ssid, SSID_MAX_LEN);
	ap->band_capabilities = prob.band_capabilities;
	ap->max_sta_num = prob.max_sta_num;
	time(&ap->last_seen);
	ap_list[ap_index] = local_ap;
	INIT_LIST_HEAD(&ap->list);
```



{: style="text-align:justify" }
###### __[8]__
On déclare une variable locale `ap_index` de type `uint8_t`. Ce type  -dans la norme ISO C99: 7.18 Integer types- de la librairie `<stdint.h>`  est défini comme suite :

```c
/* Unsigned.  */
typedef unsigned char		uint8_t;

/* Maximum of unsigned integral types.  */
# define UINT8_MAX			(255)

```
On assigne à cette variable `-1`, un entier négatif, cela n’est pas une erreur en soit car la valeur va être convertie en `unsigned` comme ceci : `UINT8_MAX + 1 -1 = 255` 
. (C standard [C99 6.3.1.3])

On aura `ap_index == 255`

{: style="text-align:justify" }
###### __[27]__
`ap_index`  reçoit  une valeur  `i` allant  soit  de `0` à `MAX_LOCAL_AP` ou reste à `255` si le test __[26]__ est faux.  
__Ps__ : MAX_LOCAL_AP == 8

###### __[31]__

{: style="text-align:justify" }
On teste `if (ap_index == -1)`, on sort de la fonction si c'est vrais. C’est la où  il y a une erreur !  
À la sortie de la boucle for, la valeur de `ap_index` est toujours testée. Le but est de sortir de la fonction complète dans le cas ou 
la condition __26__ de la boucle for est fausse (liste pleine), à ce moment la  `ap_index` n’a pas changé depuis sa définition et donc on doit sortir de la boucle. 
Le soucis est que `ap_index` ne vaudra jamais `-1` car sa valeur est convertie en `255`.  

###### __[35]__
Le test étant faux,  on alloue de la mémoire pour une structure `local_ap_info_t` qui est très grande. C’est ça qui provoque la surconsommation de la mémoire. 

###### __[48]__
On fourni à   `ap_list`  à l’index 255  l'adresse de la mémoire  alloué précédemment.   

{: style="text-align:justify" }
Cette  dernière étape est intéressante. `ap_list` est un tableau de  pointeurs dont la taille est fixée à `MAX_LOCAL_AP càd 8`.  
En C/C++, pour des buts de vitesse, il n’y a pas de bounds checking en utilisant les tableaux. On peut  accéder à un index 255 d’un tableau de 8 cases.
Le comportement dans ce cas est indéfini. C’est une bonne méthode d’attaque par Buffer Overflow.

{: style="text-align:justify" }
#### Explication

Dans le cas où le tableau est  local à une fonction, la mémoire  lui est réservée  dans le stack (pile).
Dans ce cas, déborder du tableau c'est juste accéder à une autre zone allouée dans la pile. Il n’y aura pas de segmentation fault que lorsqu’on déborde de la pile en entier.  


Voilà tout, si vous avez des remarques, n’hésitez pas en m’en faire part ! 

À une prochaine. 
