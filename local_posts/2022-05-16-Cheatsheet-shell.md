---
title: Commandes Shell utiles
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-05-16 21:17:00 +0100
categories: [Linux, Shell]
tags: [shell]
---

# 1. tail

Cette commande affiche les 10  derniere ligne d'un fichier
- -n5 : les 5 dernieres lignes
-  n+5: à partir de la ligne 5
- -c5 : les 5 derniers octets
- -f	voir ce qui a été ajouté aux fichiers 

```shell
ls -l |  tail -n5
```
Chercher les 5 derniers fichiers ou dossiers modifiés

# 2. alias
Créer un alias pour une commande 

Temporaires
: `alias nom_alias='commande'`  

Permanents
: `alias nom_alias='commande'` dans bashrc  


`alias` liste les alias  
`unalias nom_alias` pour supprimer 

# 3. variables spéciales

- **$#**	: nombre d'arguments passés à this->script
- __$*__	:affiche tout les arguments passés au script 
- **$1**	:les  arguments 1 et 2
- **$0**	:le nom du script
- **$ _**	:la derniere commande passée au shell  
- **$?**	:la commande passée s'est elle bien déroulé 
  - 0	if true 
  - 1/2	else 
- **$$**	:numero du processus actuel 

# Chercher un patern dans des fichiers

``` shell
ack "patern"
```
``` shell
grep -rnw '/path/to/file' -e 'patern'
```
- -r	: recursive
- -n	: line number 
- -w	: match all word 

# Envoyer binaire par TFTP

``` shell
(echo binary; echo put file.bin ) | tftp ip_add
```
# Redirection 

``` shell
2>&1 : stderr to stdout
&>/dev/null all to null 

```

