---
title: Script shell vicieux
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-04-05 22:07:00 +0100
categories: [Linux,Shell]
tags: [shell snmpget]
pin: true
---
{: style="text:align-justify" }
J’ai écrit un petit script bash pour interroger des clients pour savoir si un daemon (collectd)  est activé sur eux.

```bash
#!/bin/bash
ip="10 11 12 20 30 32 34 35 36 37 38 39"
ip="$ip 40 41 42 43 44 45 46 47 48 49"
ip="$ip 50 51 52 53 54 55 56 57 58 59"

for i in $ip; do
    RESULT=$(snmpget -v2c -d -D ALL -c public 192.168.2.$i .1.3.6.1.4.1.28097.10.9.1.0 | tail -1)
	sleep 1
		if  [ "$RESULT" == "iso.3.6.1.4.1.28097.10.9.1.0 = INTEGER: 2" ]; then
			echo COLLECTD ACTIVATED IN  $i
		else
			 echo NOT ACTIVATED IN $i
		fi
done
exit
```
{: style="text:align-justify"}
Comme vous le voyez c’est assez simple, j’ai une liste d’adresses IP  que je parcoure dans une boucle for. 
Dans la boucle for, je fais un `snmpget` avec l’OID  du daemon qui spécifie si dernier  est activé ou désactivé, à fin,
j’extraie la dernière ligne qui contient le retour de `snmpget`et  je fais des conditions. 

#### Commades utilisées

 - __tail -1__ : extrait la derniere ligne d'un fichier 
 - __snmpget__ : lire la MIB sur le produit et renvoyer l'information qui correspondent à l'OID.
	- __-v2c__ : utiliser la version 2 de snmp 
	- __-d__   : dump les packets  I/O en hexadecimal 
	- __-D ALL__  : mode debug  extrem verbosity 
	- __-c public__ : community string, c'est un peux  comme un  ID ou un  password pour permettre l'accés au informations du client.
	- __adresse ip  & OID__ 


 Mais alors ou est le soucis ? 
{: style="text-align:justify"}
 Le soucis est que ce programme rentre dans la condition `if [...]` que dans la dernière itération  de la boucle ! c’est à dire `ip=59`   
 La cause de ça est que `snmpget`   pollue les sortie `stderr`  avec des messages et provoque ça, en redirigeant la sortie `stderr` vers `stdout` ou  vers un fichier, ça résout le soucis.  

```bash
    RESULT=$(snmpget -v2c -d -D ALL -c public 192.168.2.$i .1.3.6.1.4.1.28097.10.9.1.0 2>&1 | tail -1)
```
<br>
- 2 : descripteur de fichier pour `stderr`
- 1 : descripteur de fichier pour `stdout`
- '>' oprateur de redirection.
- & spécifie qu'on redirige la sortie vers un descripteur de fichier.

<p>Voila voila, c'etait chiant ! </p>

# Redirection shell 

``` shell
2>&1 : sterr to stdout 
&> /Dev/NULL: les deux


```
