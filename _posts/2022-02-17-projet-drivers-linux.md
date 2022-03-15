---
title: Projet de Drivers Linux 
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-02-17 22:27:00 +0100
categories: [Linux, Drivers]
tags: [drivers linux, linux drivers]
pin: true
---


{: style="text-align: justify" }
Quand j’ai commencé à m’intéresser aux drivers Linux, on m’avait  orienté vers le livre que j’ai présenté
[ici]({% link _posts/2022-02-10-Mes-livres-linux.md %}),  Linux Device Drivers  de  Jonathan Corbet, Alessandro Rubini et Greg Kroah-Hartman.<br>

{: style="text-align: justify" }
Ce livre est une référence dans le domaine. Les auteurs -dont l’actuel mainteneur de la branche stable  Greg Kroah-Hartman-  ont essayé  de rester le plus
indépendant possible du hardware, la seule chose dont on avait besoin pour  utiliser ce livre et développer ses codes 
est un ordinateur. Pour y arriver, ils ont développé tout les drivers sur la RAM, c’est à dire simuler le device sur une plage de mémoire vive
de l’ordinateur. C’est une excellente méthode car elle ne demandait pas au lecteur de se munir de tel ou tel périphérique pour pouvoir apprendre.<br>

{: style="text-align: justify" }
Néanmoins, quand j’ai fait mon stage chez [Tiempo Secure](https://www.tiempo-secure.com) et que j’ai eu comme tache de développer un driver pour un composant (cliquez ici) ; je me suis confronté à un grand soucis. J’avais appris avec le livre les bases et les méthodes nécessaires, mais je n’arrivais pas à voir le
gap qu’il y avait entre un driver sur la RAM et le driver d’un vrais device. En Gros, j’avais une idée  du fonctionnement des  drivers Linux,
mais je ne savais pas par où commencer à développer le mien.<br>

{: style="text-align: justify" }
Ce gap (écart) était pour moi le type de mon driver, je n’arrivais pas choisir un type de driver à mon device et  l’API kernel à utiliser : j’étais perdu !<br>

{: style="text-align: justify" }
Suite à ça, j’ai eu comme idée  décrire des drivers pour  quelques devices. Le but est d’utiliser les enseignements du livre 
et ma petite expérience afin d’expliquer la façon  d’appréhender le driver d’un réel device  pour la première  fois.  Donc ici,
je vais être très dépendant du hardware.  J’expliquerai toutes les étapes les plus importantes et donner une vue synthétique et complète 
de chaque driver, et  surtout ! éviter aux autres de tomber dans les mêmes pièges que moi.<br>

{: style="text-align: justify" }
J’ai acheté des capteurs sur Ali Axpress, je vais en sélectionner 10 qui vont englober au  maximum le contenu du livre et différentes librairies 
et API  kernel à utiliser. Sur chaque chapitre du livre, je vais essayer de mettre en pratique les connaissances acquises  afin de mieux comprendre le sujet. <br>

{: style="text-align: justify" }
Voila pour l’essentiel.  J’ai déjà en tête le premier driver à faire  et le device à utiliser. Rendez vous au prochain épisode !

