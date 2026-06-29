---
title: Build Yocto sur Raspberry-Pi 4 [Act 3]
author:
name: Aliliche larbi
link: https://github.com/Aliliche
date: 2026-03-26 00:01:00 +0100
categories: [Yocto, ]
pin: true
---

# Configuration par default de l'Os linux

Le système compilé vient avec sa propre config par default: IP, Gateway, Password ..etc.  
Pour changer cette config, il faut comprendre l'achitecture des layers:


## Architecture des couches Yocto (Poky)

#### 1. `meta` (openembedded-core) — Le cœur

- **Rôle :** Fournit la base minimale d’un système Linux embarqué.
- **Contenu :**
  - Bibliothèques essentielles (`glibc`)
  - Outils de base (`busybox`)
  - Infrastructure de build (classes, recettes fondamentales)
- **Objectif :** Rester léger, générique et réutilisable.

---

#### 2. `meta-poky` — La distribution

- **Rôle :** Définit la configuration de la distribution Poky.
- **Contenu :**
  - Fichiers `.conf` (distro, options, politiques)
  - Quelques recettes secondaires
- **Objectif :** Définir l’identité technique du système (format des paquets, options, etc.).

---

#### 3. `meta-openembedded` — L’extension

- **Rôle :** Apporter des logiciels additionnels non essentiels.
- **Structure :** Ensemble de couches (`meta-oe`, `meta-python`, `meta-networking`, etc.)
- **Objectif :** Garder le core propre et permettre une forte modularité.

---

#### 4. Couches matérielles (ex: `meta-raspberrypi`)

- **Rôle :** Support matériel spécifique.
- **Contenu :**
  - Kernel adapté
  - Firmware de boot
  - Configurations machine

---

#### Résumé

| Couche               | Rôle principal                          |
|---------------------|------------------------------------------|
| `meta`              | Base système + infrastructure            |
| `meta-poky`         | Configuration de la distribution         |
| `meta-openembedded` | Logiciels additionnels                   |
| `meta-*` (hardware) | Support matériel (ex: Raspberry Pi)      |

# SSH et Adresse IP.

On peut changer l'adresse IP de l'os dans /etc/networking/interfaces, les modifs sont temporaires, c'est un network manager appelé ifupdown.  
La distribution compilée vient avec un autre network manager appelé connman. Il est dans meta.

J'ai créer un layer appelé meta-mangrove, dedans je surcharge recipes-connectivity qui est la recette poky contenant le daemon connman.

```
vicious@debian:~/git/learn_yocto/meta-mangrove$ ls
conf  COPYING.MIT  README  recipes-connectivity  recipes-example

vicious@debian:~/git/learn_yocto/meta-mangrove/recipes-connectivity/connman$ ls
connman  connman_%.bbappend
```
Le fichier connman_%bbappend. 
👉 le % est un wildcard (joker) qui signifie :“applique ce .bbappend à toutes les versions de la recette connman”:

``` bash
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"

SRC_URI += "file://wired.config" 

do_install:append() {
    install -d ${D}/var/lib/connman
    install -m 0600 ${WORKDIR}/wired.config ${D}/var/lib/connman/
}
```
#### 👉 NOTE:
La distribution yocto est Dunfell, j'utilise la syntaxe moderne yocto.

```python
do_install_append() { ... } # Ancienne syntaxe utilsée sur meta-raspberrypi
do_install:append() { ... } # Migration vers Yocto récent,supportée par Dunfell

```
le dossier connman contient le fichier wired.config. 

```
[global]
Name = Wired
Description = Wired network configuration

[service_eth0]
Type = ethernet
DeviceName = eth0
IPv4 = 192.168.20.20/24
Nameservers = 8.8.8.8
```
#### Explications :

- [service_eth0] : nom arbitraire (doit commencer par service_).
- Type = ethernet : obligatoire.
- DeviceName = eth0 : plus simple, plus fiable sur Raspberry Pi (pas besoin de connaître le MAC à l’avance).On peut mettre MAC = xxxxx
- IPv4 = IP/masque/gateway → le masque peut être en notation CIDR (/24) ou en notation pointée (255.255.255.0).
- Nameservers : optionnel mais fortement recommandé.

## Recompilation de connman


``` bash
bitbake connman -c clean
bitbake connman
bitbake rpi-test-image
```


