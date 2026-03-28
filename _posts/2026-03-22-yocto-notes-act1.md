---
title: Build Yocto sur Raspberry-Pi 4 [Act 1]
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2026-03-22 20:39:00 +0100
categories: [Yocto, ]
pin: true
---

# Dépots et clonage

## Stable release Dunfell Avril 2020, Yocto 3.2 Poky V23

```bash
$ git clone -b dunfell git://git.yoctoproject.org/poky.git
```

Faire un pull périodique. 

## Creer un dossier build
```bash
 source poky/oe-init-build-env 
```
Initialiser l’environnement de build Yocto (OpenEmbedded) pour pouvoir lancer une compilation.  
Ce fichier doit etre sourcé à chaque fois qu'il faut travailler sur ce projet.  
Sourcer  avec le nom du dossier built.


####  Ce que fait la commande

- Configure les variables d’environnement nécessaires :
  - `PATH`
  - `BBPATH`
  - `BUILDDIR`
- Prépare l’environnement BitBake
- Crée automatiquement le répertoire `build/` (s’il n’existe pas)
- Positionne le terminal dans ce répertoire

## Dans le dossier /build, un dossier /conf sera crée:

- blayers.conf : 
    - Définit les layers utilisés par le build
    - Liste des chemins vers les layers (ex: `meta`, `meta-raspberrypi`, etc.)

- local.conf : 
    - Configuration locale du build (spécifique à la machine)
    - `MACHINE` (ex: `raspberrypi4`)
    - `DL_DIR`, `SSTATE_DIR`
    - Options de compilation (parallélisme, debug, image features…)
    - Contrôle le comportement global du build
    - Personnalisation sans toucher aux layers

## Résumé rapide

- `bblayers.conf` → **quoi utiliser (layers)**
- `local.conf` → **comment builder (config)**
- `templateconf.cfg` → **comment initialiser (template)**

# 🧱 Concepts fondamentaux Yocto

---

### 🐧 Poky
Distribution de référence Yocto.

- Combine :
  - **BitBake** (moteur de build)
  - **OpenEmbedded-Core (OE-Core)** (recettes de base)
  - Config par défaut
- Sert de point de départ pour créer une distro custom

---

### 🧩 Layers (couches)
Empilement modulaire de fonctionnalités.

- Contiennent :
  - recettes
  - configurations
  - patches
- Exemple :
  - `meta` (core)
  - `meta-raspberrypi`

👉 Permet de séparer proprement les responsabilités

---

### 🍳 Recettes (`.bb`)
Instructions pour construire un paquet.

- Décrivent :
  - source (URL, git…)
  - dépendances
  - étapes de build (`do_compile`, `do_install`)
- Exemple : compiler un kernel, une lib, une app

---

### 📦 BitBake
Moteur de build.

- Interprète les recettes
- Gère :
  - dépendances
  - tâches
  - ordre d’exécution



