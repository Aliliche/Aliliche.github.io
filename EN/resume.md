---
layout: page
title: My Resume
icon: fas fa-file-alt
order: 4
permalink: /resume-en/
---
{% capture experience_content %}
{: style="text-align : justify" }
Passionate about low-level development and integration, I work across the entire embedded Linux stack:

- **Application development**: bootloader (U-Boot), kernel, init, daemons.
- **Porting and integration**: toolchains, development environments, BSPs, custom Linux systems.
- **Embedded platform optimization**: boot time (U-Boot, kernel, init), memory footprint.

My versatility allows me to work across all software layers, while focusing on issues close to the hardware. I can also work on the lower layers of Android (kernel, init, daemons, bootloader).
{: style="text-align : justify" }

<div class="contact-button-container">
  <a href="{{ '/about/' | relative_url }}" class="contact-button">
    Contact me
  </a>

  <a href="{{ '/about/' | relative_url }}" class="contact-button">
    (PDF)
  </a>
    <p class="language-switch">
    <a href="{{ '/resume/' | relative_url }}">🇫🇷 Français (FR) →</a>
    </p>
</div>

{% endcapture %}
{% include experience.html content=experience_content %}

---
# Detailed Professional Experience
---

{% capture experience_content %}
## **Embedded Linux Engineer**
**Wizzilab** — Montrouge, France
*April 2026 — Present*

- **Wizzilab gateway production:** took over, simplified and enhanced programming tools. Set up a dedicated machine and trained engineers in the automated production process. This covers programming blank SoCs, registering gateways on servers, pre- and post-production testing and validation, and preparation for delivery.
- Maintained the gateways' Yocto distribution on the Variscite platform: maintained the `meta-variscite` layers and the platform-specific `u-boot-imx` bootloader.
- Debugged and fixed boot bugs and crashes occurring after `OTA upgrade` updates.
- Fixed various bugs through to product stabilization.

🛠 **Technical environment**: C, shells (sh/bash), Ruby, Lua, Yocto, U-Boot, embedded Linux, Variscite, i.MX6UL, DASH7.
{% endcapture %}
{% include experience.html content=experience_content %}

---

{% capture experience_content %}
## **Embedded Linux Engineer**

**ACKSYS Communications & Systems** — Yvelines, France
*November 2021 — April 2026*

Developed, enhanced and maintained **WaveOS, an embedded Linux system based on OpenWrt**, working across the entire software stack, from the `u-Boot` bootloader and `device tree` through the kernel to services and embedded applications.

- **Wi-Fi and roaming:**
    - Ported the roaming strategy to OpenWrt 24 / Linux 6.6, adapting the entire Wi-Fi stack (`iw`, `iwinfo`, `hostapd`, `wpa_supplicant`, `mac80211`, `ath11k`).
    - Debugged and performed in-depth analysis of the `ath11k` driver (Copy Engine, WMI, MAC, Data Path) and the `mac80211` layer. Made changes that reduced roaming time from **1 s to approximately 150 ms**.
    - Analyzed and validated Qualcomm firmware after modifications by a subcontractor. Set up a dedicated test station and used Wireshark to analyze `powersave` frames and various `ath11k` parameters affecting the behavior of `beacon`, `idle`, `scan`, etc.
    - Identified bugs in proprietary Qualcomm firmware affecting roaming and VLAN frame handling. Reported the issue to the mainline project.

- **Predictive roaming:**
    - Developed features for ACKSYS and ATOS/NOKIA projects and modified the predictive roaming algorithm to improve air-to-ground communication throughput.

- **Userspace development:**
    - Developed `surveyd`, a monitoring daemon to detect and handle Wi-Fi link losses.
    - Enhanced and maintained init services, net-snmp, hostapd, supplicant, telemetry, monitoring (`collectd`), and the OTA update system.

- **Bootloader and system reliability:**
    - Developed a dual-boot mechanism in `u-Boot`, supporting booting from multiple partitions and recovery after a crash or corruption.
    - Developed the GUI page used to program WaveOS and U-Boot with Flasher and switch between partitions.
    - Managed system configurations across partitions.
    - Set up a programming station for blank or corrupted SoCs using Qualcomm's `sahara` tool.

- **Embedded web interface:**
    - Designed and implemented a new WebUI for ACKSYS routers, migrating from Lua to a fully JavaScript-based architecture.
    - Rewrote dozens of ACKSYS service configuration pages in JavaScript.

- **Embedded Linux:**
    - Performed board bring-up for the ACKSYS AirWave series: device tree, U-Boot, kernel, GUI.
    - Measured and improved boot time and memory footprint for the ACKSYS AirBox series.
    - Modified the GPY215 PHY driver on RailBox to handle autonegotiation.
    - Integrated and modified the DS2411 driver for serial number management.
    - Modified drivers for LEDs, GPIO, athxk and Ethernet PHYs.
    - Developed features and debugged and maintained functionality and services across WaveOS, including ubus, collectd, net-snmp, VLAN bridging, init, wpa_supplicant and hostapd.

- **AirWave series security assessment:**
    - Analyzed security gaps in ACKSYS products, particularly the lack of Secure Boot.
    - Assessed hardware and software security solutions: Secure Boot, TPM and ARM TrustZone.
    - Conducted in-depth technical research, gathered information from suppliers and compared costs.
    - Formalized requirements and wrote a technical specification, which was presented at a meeting for evaluation.

🛠 **Technical environment**: C, shells (sh/bash), Python, Lua, JavaScript, U-Boot, kernel, mac80211, ath11k, Wi-Fi, TCP/IP, embedded Linux, Secure Boot, TPM, IPQ6010, Qualcomm, NAND/NOR, UBI, MTD.
{% endcapture %}
{% include experience.html content=experience_content %}

---

{% capture experience_content %}
## **Intern — Linux Driver Developer**
**Tiempo Secure** — Grenoble, France
*February 2021 — July 2021*

**1. MTD driver for S25FL Flash memory and block driver for MMC**
- Implemented the basic driver structure on the TIEMPO Secure Enclave.
- Used the UBI API (UBI File System) for wear leveling and garbage collection.

**2. Platform driver for TIEMPO IPC (FPGA Inter-Processor Communication)**
- Defined the driver's technical specifications.
- Analyzed and validated the IP in bare-metal mode before developing the driver.
- Developed the Linux platform driver: Device Tree, interrupt handling, probe function, character-device interface and mailbox management.
- Created a userspace test tool.
- Integrated the driver into Buildroot.

🛠 **Technical environment**: C, assembly, shells (sh/bash), Buildroot, U-Boot, embedded Linux, FPGA, RISC-V, kernel, drivers, SPI, kgdb, NAND/NOR, UBI, MTD.
{% endcapture %}
{% include experience.html content=experience_content %}

---

{% capture experience_content %}
## **Final-Year Project**
**Grenoble University Space Center** — Grenoble, France
*November 2020 — February 2021*

Developed a small FreeRTOS application on a MicroBlaze soft core:
- Acquired data from FPGA memory and transmitted it using the TCP/IP stack (Lightweight TCP/IP).
- Implemented the MicroBlaze IP hardware blocks and the software application.

🛠 **Technical environment**: C, Bash, FreeRTOS, TCP/IP, FPGA, Xilinx, Artix-7.
{% endcapture %}
{% include experience.html content=experience_content %}

---
# Education
---

{% capture experience_content %}
## Master’s Degree (Year 2) — Microelectronics, Embedded Real-Time Systems Integration
**Université Grenoble Alpes**
*September 2019 — August 2021*

- Embedded systems
- RTOS: interrupts, scheduling, algorithms, optimization (FreeRTOS, PREEMPT-RT)
- Modules and drivers
- Computer architecture and execution models

## Master’s Degree (Year 1) — Electronic Instrumentation
**Mouloud Mammeri University of Tizi Ouzou**
*September 2014 — August 2018*

{% endcapture %}
{% include experience.html content=experience_content %}

---
# Languages
---
{% capture experience_content %}

- **English, French, Arabic** — fluent
- **Kabyle** — native language

{% endcapture %}
{% include experience.html content=experience_content %}

---
# Skills
---

{% capture experience_content %}
**Embedded Linux**
- Advanced proficiency in embedded Linux, particularly Linux wireless and OpenWrt: init, configuration, partitions, upgrades.
- Board bring-up for new boards. Excellent understanding of the MTD and UBI subsystems and NAND/NOR flash memory.
- Toolchains, cross-compilation and Linux BSPs: crosstool-NG, Yocto, Buildroot, U-Boot.
- Linux driver development: character, block, MTD, platform and SPI drivers.
- Driver development for FPGA blocks (RISC-V architecture).
- Good understanding of the TCP/IP and Wi-Fi stacks (ath11k/ath10k, mac80211, bridge, VLAN, ARP).
- Reading datasheets and hardware schematics.

**Manufacturing**
- Good knowledge of manufacturing processes in France, certifications and interactions with international customers.
- Testing, validation and bug management in a production environment.

**Programming languages**
- C, Shell, RISC-V assembly, Lua, HTML/CSS, JavaScript, Python, Ruby.

**Tools**
- GNU/Linux: make, gcc, gdb, kgdb, Valgrind, ftrace.
- Yocto, Buildroot, crosstool-NG, git, quilt, Redmine, Vim.
- Wireshark, tcpdump, iperf.

**Protocols**
- Wi-Fi, TCP/IP, UART, I2C, SPI, USB, RS-232.
- Good understanding of the OSI model and the Wi-Fi stack.

{% endcapture %}
{% include experience.html content=experience_content %}

---
# Projects and Open-Source Contributions
---

{% capture experience_content %}
- **Participation in the French Robotics Cup** — *May 2021*
  Theme: *Sail the World* (Grenoble)

- **Open-source contributions and events**
  Participation in events and conferences on embedded systems and free/open-source software:
  **Paris Embedded, FOSDEM, Hack in Paris, SysDream**

{% endcapture %}
{% include experience.html content=experience_content %}

---
# Other
---

{% capture experience_content %}
**Hobbies**
- Motorsport, sim racing
- Photography: [click here](https://www.instagram.com/z.buffer_/)
- Video game analysis
- Retro computing and retrogaming
- 3D
- Member of the MO5 Association
- Mindfulness meditation, Japanese culture, travel (🇯🇵 🇫🇷 🇭🇺 🇦🇹 🇩🇪 🇧🇪 🇵🇹 🇪🇸 🇨🇭)

{% endcapture %}
{% include experience.html content=experience_content %}
