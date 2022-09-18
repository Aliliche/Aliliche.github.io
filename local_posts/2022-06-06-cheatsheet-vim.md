---
title: Commandes VIM utiles
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-06-06 18:36  +0100
categories: [Linux, VI]
tags: [vi]
---


# Remplacer des mots

``` shell
:/s/mot/Remplaçant/ : premiere occurence de mot
:/s/mot/Remplaçant/g : toutes occurence de mot dans la ligne courante 
:%s/mot/Remplaçant/g : toutes les  occurence de mot dans tout le fichier
:3,10s/mot/Remplaçant/g : toutes les  occurence de mot entre les lignes 3 et 10 incluses
```

