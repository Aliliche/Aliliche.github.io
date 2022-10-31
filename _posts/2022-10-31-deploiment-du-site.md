---
title: Erreur de déploiment du site avec Github Actions
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-10-31 14:37:00 +0100
categories: [Jekyll site]
tags: [workflow, Github Actions]
pin: true
---


{: style="text-align:justify"}

Quand on déploie un site avec Github Actions, il se peut que le workflow échoue pour plusieurs raisons, 
ça m’est arrivé et voici les causes dans mon cas : 


1.  Quand essaie de renommer le repo git du site pour changer l’URL, le workflow va échouer.  
Sans un nom de domaine spécifique, 
les sites sur github doivent toujours êtres de la sorte : `USER.github.io`  ou `USER` est le `username`   sur github. 
Donc si on renomme le repo du site , on doit aussi renommer le `username`   


2. Parfois les pages n’apparaissent pas. Dans mon cas j’ai pushé le fichier `Gemfile.lock` sur la branche et ça a cassé le workflow,
donc ce fichier ne doit pas être sur la branche. 


3. Il se peut que ça échoue car le worflow  n’est pas bon ou il manque des choses.Il Faut prendre un workflow qui fonctionne comme celui que j’ai utilisé, [voici le lien.](https://github.com/Aliliche/Aliliche.github.io/blob/main/.github/workflows/pages-deploy.yml) 

🎃 ⚰️  🧛  Thats it !! 
