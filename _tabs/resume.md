---
title: CV/Resume
icon: fas fa-file-alt
order: 4
permalink: /resume/ 
---

{% capture experience_content %}

<p class="language-switch">
    <a href="{{ '/resume-en/' | relative_url }}">🇬🇧 English (EN) →</a>
</p>

{: style="text-align : justify" }
Passionné par le développement et l’intégration bas niveau, j’interviens sur l’ensemble de la chaîne Linux embarqué: 

- **Développement d'application**:  bootloader (U-boot), kernel, init, daemons.  
- **Portage et intégration**: toolchains, environnements de développement, BSP, Linux custom.  
- **Optimisation des plateformes embarquées**: Vitesse de démarrage (U-boot, kernel, init), empreinte mémoire.  

Ma polyvalence me permet de travailler sur toutes les couches logicielles, tout en privilégiant les problématiques proches du matériel. Je peux également intérvenir sur les couches basses d’Android (kernel, init, daemons, bootloader).
{: style="text-align : justify" }

<div class="contact-button-container">
  <a href="{{ '/about/' | relative_url }}" class="contact-button">
    Me contacter
  </a>
  
  <a href="{{ '/assets/pdf/ALILICHE_LARBI_Embedded_Linux.pdf' | relative_url }}" 
  class="contact-button" download>
    PDF
  </a>
</div>

{% endcapture %}
{% include experience.html content=experience_content %}

---
# Parcours professionnel détaillé
---

{% capture experience_content %}
## **Ingénieur Linux Embarqué**
**Wizzilab** — Montrouge, France
*Avril 2026 — Aujourd’hui*

- Production des gateways Wizzilab : reprise, simplification et évolution des outils de programmation des gateways. Mise en place d'une machine dédiée et formation des ingénieurs au processus de production automatisé. Cela couvre la programmation des SoC vierges, l'enregistrement des gateways sur les serveurs, tests et validation avant et après production, ainsi que la préparation à la livraison.
- Maintenance de la distribution Yocto des gateways sur plateforme Variscite : maintenance des couches `meta-variscite` ainsi que  du  bootloader  `u-Boot-imx` spécifique à la plateforme
- Débogage et correction de bugs de démarrage et de crashs survenant après les mises à jour `OTA upgrade`.
- Correction de bugs divers jusqu'a la stabilisation du produit.

🛠 **Environnement technique** :C, shells (sh/bash), Ruby, Lua, Yocto, U-Boot, Linux embarqué, variscite, IMX6ul, dash7.

{% endcapture %}
{% include experience.html content=experience_content %}

---

{% capture experience_content %}
## **Ingénieur Linux Embarqué**

**ACKSYS Communications & Systems** — Yvelines, France
*Novembre 2021 — Avril 2026*

Développement , évolution, et maintenance  de **WaveOS, un Linux embarqué basé sur OpenWrt**, avec des développements sur l’ensemble de la pile logicielle, du bootloader `u-Boot` au `device tree` jusqu’au Kernel, aux services et applications embarquées.

- **Wi-Fi et roaming :** 
    - Portage de la stratégie de roaming vers OpenWrt 24 / Kernel 6.6, avec adaptation de toute la stack Wi-Fi (`iw`, `iwinfo`, `hostapd`, `wpa_supplicant`, `mac80211`, `ath11k`).  
    - Debug et analyse approfondie du driver `ath11k` (Copy Engine, WMI, MAC, Data Path) et la couche `mac80211`. J'ai apporté des modifications permettant de réduire le temps de roaming de **1 s à ~ 150 ms**.
    - Analyse et validation de firmwares Qualcomm après leur modification par un sous-traitant. Mise en place d'une station dediée et analyse par wireshark des trames de `powersave`, de differents parametres de `ath11k`  permettant de modifier le comportement de `beacon`,`idle` `scan` ..etc
    - Mise en évidance de bugs dans les firmwares proprietaires Qualcomm dans la gestion du roaming, des trames vlan. Sujet remonté au mainline.

- **Roaming prédictif :**   
    - Développement de fonctionnalités pour les projets ACKSYS & ATOS/NOKIA, modification de l'algorithme de roaming predictif pour ameliorer le débit de la communication bord-sol


- **Développement userspace :**   
    - Développement de « surveyd », un daemon de monitoring pour détecter et gérer les pertes de lien Wi-Fi.  
    - Evolution et maintenance des services init, net-snmp, hostapd, supplicant, télémétrie, supervision (`collectd`) et du systeme des mises à jour OTA.

- **Bootloader et fiabilité système :**   
    - Développement d’un mécanisme de démarrage dual-boot sous `u-Boot`, démarage sur plusieurs partitions, récupération après crash ou corruption  
    - Developpement de la page GUI permettant de programmer Flasher WaveOS et u-boot, de switcher entre les partitions.  
    - Gestion des configs système entre paritions.
    - Mise au point d'une station de prorgammation des socs vierges ou corrompus avec l'outil `sahara` de chez Qualcomm.


- **Interface Web embarquée :**   
    - Conception et implémentation d’une nouvelle WebUI pour les routeurs ACKSYS, migration de Lua vers une architecture full JavaScript.
    - Réécriture de dizaines de pages de configuration des services ACKSYS en JavaScript.

- **Linux embarqué :**   
    - Board bring-up de la serie ACKSYS airwave : device tree, u-boot, kernel, GUI.
    - Mesure et amélioration du temps de démarage et de l'empreinte mémoire sur la serie ACKSYS airbox.
    - Modification du driver phy gpy215 sur railbox pour la gestion de l'autoneg. 
    - Intégration et modification du driver ds2411 pour la gestion des numero de serie.
    - Modification de drivers: leds, gpio, ath11k, ethernet phy..
    - Developpement de features, debug, maintenance  de fonctionnalités et de services sur l’ensemble de WaveOS: ubus, collectd, net-snmp, bridge vlan, init, wpa_supplicant, hostapd

- **Étude de sécurisation de la serie airwave**
    - Analyse des lacunes de sécurité des produits ACKSYS, notamment l’absence de Secure Boot.
    - Etude des solutions de sécurisation hardware/software: Secure Boot, TPM et ARM TrustZone, Qualcommn QFPROM.
    - Recherche technique approfondie, collecte d’informations auprès de fournisseurs et étude comparative des coûts.
    - Formalisation des besoins et rédaction d’une spécification technique, présentée en réunion pour évaluation.


🛠**Environnement technique** :C, shells(sh/bash), python, lua, JavaScript, u-Boot, kernel, mac80211, ath11k, Wi-Fi, TCP/IP, Linux embarqué, Secure Boot, TMP, ipq6010, Qualcommn, nand/nor, ubi, mtd.
{% endcapture %}
{% include experience.html content=experience_content %}


---

{% capture experience_content %}
## **Stagiaire – Développeur Driver Linux**
**Tiempo Secure** — Grenoble, France
*Février 2021 — Juillet 2021*

**1. Driver MTD pour mémoire Flash S25FL et Block Driver pour MMC**
- Implémentation de la structure de base du driver sur Le Secure Enclave TIEMPO.
- Utilisation de l’API UBI (UBI File System) pour le wear leveling et garbage collection.

**2. Platform Driver pour TIEMPO IPC (FPGA Inter-Processor Communication)**
- Définition des spécifications techniques du driver.
- Analyse et validation de l’IP en bare-metal avant développement du driver.
- Développement du platform driver Linux : Device Tree, gestion des interruptions, fonction probe, interface char et gestion des mailboxes.
- Création d’un outil de test en userspace.
- Intégration Buildroot.

🛠 **Environnement technique** :C, asm, shells(sh/bash), buildroot, U-Boot, Linux embarqué, FPGA, RISCV, kernel, drivers, SPI, kgdb, nand/nor, ubi, mtd.
{% endcapture %}
{% include experience.html content=experience_content %}

---

{% capture experience_content %}
## **Projet de Fin d’Études**
**Centre Spatial Universitaire de Grenoble** — Grenoble, France
*Novembre 2020 — Février 2021*

Développement d’une petite application FreeRTOS sur soft-core Microblaze :
- Acquisition de données depuis la mémoire FPGA et envoi via pile TCP/IP (Lightweight TCP/IP).
- Implémentation hardware des blocs IP Microblaze + application logicielle.

🛠 **Environnement technique** :C, Bash, FreeRTOS, TCP/IP, FPGA. Xilinx, artix7.
{% endcapture %}
{% include experience.html content=experience_content %}

---
# Education
---

{% capture experience_content %}
## Master II – Micro-électronique, Intégration des systèmes temps réels embarqués
**Université Grenoble Alpes**
*Septembre 2019 — Août 2021*

- Systèmes embarqués
- RTOS : interruptions, ordonnancement, algorithmes, optimisation (FreeRTOS, PREEMPT-RT)
- Modules et drivers
- Architecture ordinateur et modèles d’exécution

## Master I – Instrumentation Électronique
**Université Mouloud Mammeri Tizi Ouzou**
*Septembre 2014 — Août 2018*

{% endcapture %}
{% include experience.html content=experience_content %}

---
# Langues
---
{% capture experience_content %}

- **Anglais, Français, Arabe** — courant
- **Kabyle** — Langue maternelle

{% endcapture %}
{% include experience.html content=experience_content %}

---
# Compétences
---

{% capture experience_content %}
**Linux Embarqué**
- Maîtrise avancée des Linux embarqués particulièrement Linux Wireless (OpenWrt): init, config, partitions, upgrade.
- Board bring-up de nouvelles cartes. Exellente comprehention des subsystem mtd, ubi et des mémoires nand/nor.
- Toolchains, cross-compilation et BSP Linux : crosstool-NG, yocto, buildroot, u-Boot
- Développement de drivers Linux : char, block, mtd, plateform, spi.
- Développement de drivers pour blocs FPGA (architecture RISC-V)
- Bonne compréhension de la stack TCP/IP et Wi-Fi (ath11k/ath10k, mac80211, bridge, vlan, arp)
- Lecture de datasheet, schémas hardware.

**Manufacturing**
- Bonne connaissance des processus de production en France, certifications et interaction avec clients internationaux
- Tests, validation et gestion des bugs en environnement de production

**Languages de programmation**
- C, Shell, RISC-V asm, Lua, HTML/CSS, JavaScript, Python, ruby

**Outils**
- GNU/Linux : make, gcc, gdb, kgdb, valgrind, ftrace.
- YOCTO, buildroot, crosstool-NG, git, quilt, redmine, vim.
- Wireshark, tcpdump, iperf.

**Protocoles**
- Wi-Fi, TCP/IP, UART, I2C, SPI, USB, RS-232.
- Bonne compréhension du modèle OSI et de la stack WI-FI.

{% endcapture %}
{% include experience.html content=experience_content %}

---
# Projects and Open-Source Contributions
---

{% capture experience_content %}
- **Participation à la coupe de France de robotique** — *Mai 2021*
  Thème : *Sail the World* (Grenoble)

- **Contributions Open Source & Événements**
  Participation à des événements et conférences sur les systèmes embarqués et le logiciel libre/open-source :
  **Paris Embedded, FOSDEM, Hack in Paris, SysDream**

{% endcapture %}
{% include experience.html content=experience_content %}

---
# Autres
---

{% capture experience_content %}
**Hobbies**
- Sport auto, Sim Racing
- Photographie : [cliquez ici](https://www.instagram.com/z.buffer_/)
- Analyse de jeux vidéo
- Retro Computing & Retrogaming
- 3D
- Membre de l’Association MO5
- Méditation de pleine conscience, culture japonaise, voyages(🇯🇵 🇫🇷 🇭🇺 🇦🇹 🇩🇪 🇧🇪 🇵🇹 🇪🇸 🇨🇭)

{% endcapture %}
{% include experience.html content=experience_content %}
