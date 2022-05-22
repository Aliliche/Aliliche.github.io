---
title: Commandes Bash utiles
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-05-16 21:17:00 +0100
categories: [Bash cheatsheat]
tags: [commandes bash]
pin: true
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
