---
title: Build Yocto sur Raspberry-Pi 4 [Act 4]
author:
name: Aliliche larbi
link: https://github.com/Aliliche
date: 2026-04-06 00:23:17 +0100
categories: [Yocto, ]
pin: true
---

# Premiers programmes

## User Space

Dans meta-mangrove, j'ai crée un recipes-daemons pour des daemons à utiliser plus tard.

```
vicious@debian:~/git/learn_yocto/meta-mangrove/recipes-daemons/collectk$ ls
collectk  collectk_1.0.bb
```
Deux conventions existent, soit on met les sources dans files, soit dans un dossier portant le nom du daemon.  

Recette collectk_1.0.bb:


```bash
DESCRIPTION = " collectk is a simple daemon that collect kernel sensors data in real time"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://main.c;md5=b312bbb0ac926997fde80e2bff418c6d"


SRC_URI = "file://main.c"
S = "${WORKDIR}"

do_compile() {
        ${CC} ${CFLAGS} ${LDFLAGS} -o collectk main.c
}


do_install() {
        install -d ${D}${bindir}
        install -m 0755 collectk ${D}${bindir}/collectk
}

```
#### Explication:
- Ici : ${WORKDIR} correspond au répertoire temporaire créé par Yocto pour cette recette. C'est variable indiquant le répertoire source principal. Toutes les commandes de compilation seront exécutées dans ce dossier.

- install -d ${D}${bindir}: Crée le dossier /usr/bin dans le répertoire temporaire d’installation de Yocto, si ce n’est pas déjà fait.
    - ${D} : variable Yocto qui représente le répertoire temporaire d’installation pour cette recette.
    - ${bindir} : variable qui pointe vers le dossier des exécutables, souvent /usr/bin.

- install -m 0755 hello ${D}${bindir}/collectk:
    - Copie l’exécutable collectk dans /usr/bin/collectk et donne les permissions rwxr-xr-x

Le dossier collectk doit contenir les sources


#### 👉 NOTE:
Ne pas oublier de rajouter le daemon dans les IMAGE_INSTALL, cette variable de conf/local.conf  définit la liste des packages
qui doivent etres installés:

```
IMAGE_INSTALL_append = " collectk"
```

### Compilation
```
bitbake collectk
```

## Utilisation de devtool

devtool est une surcouche de bitbake qui permet de gagner du temps dans les developpement :
devtool simplifie 3 choses essentielles:

- Modifier un package existant
- Ajouter un nouveau logiciel
- Tester rapidement sans rebuild complet

## Modifier un package existant

```
devtool modify collectk
```
Crée un workspace et clone les sources de collectk dedans. C'est un en quelque sorte un repo git de suivis.
Cela permet de travailler sur une autre version que l'original, de générer des patches..

```
devtool build collectk
```

```
devtool deploy-target collectk root@192.168.20.20
```
copier le binaire dans la target.

```
devtool finish collectk meta-mangrove
```
Génère :

-   patchs propres
-   recette mise à jour

Abondonner :
```
devtool reset collectk
```

👉 NOTE: le dossier workflow est ajouté dans dans bblayer.conf, le supprimer implique
une supression dans bblayer.conf sous peine d'erreurs.
