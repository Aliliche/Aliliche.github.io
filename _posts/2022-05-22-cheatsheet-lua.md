---
title: CheatSheet LUA
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-05-22 15:02:00 +0100
categories: [CheatSheet,lua]
---


# Variables

```lua
a = 5
b = 9
a,b = 0,1
print(a+b)
```
# Typage dynamique 
La variable connait son type en fonction de la valeur ou de l'objet qui lui ai  assigné.

``` lua
a = 1
a = {nom = "monkey"}
```
On peut obtenir le type d'une variable grace à `type()`  
`print(a,type(a))`

# Concatenation

``` lua
age = 25
nom = "monkey"
print(" Je suis "..nom.."j'ai \t"..age.."ans")
```

# Casse 
- Sensible 

# Incrémentation 

``` lua
age = age + 1
```

# Fonctions 

``` lua
function foo(nom)
	prenom = "linux"
	print("hello "..nom)

	return nom.." "..prenom 
end 

-- autre methode : assigner function() à une variable.
x = function() 
		print("hello)
	end
print(x())

```
On peut renvoyer plusieurs variables dans une fonction. 

``` lua
function foo(a,b)
	return math.pow(a,b),math.pow(c*b,a)
end

print(foo(2,7))
```
On peut renvoyer  dans une table 
`tab = {foo(2,3)}` et utiliser `pairs()` pour la lire.

Dans une fonction qui renvoit plusieurs valeurs, on peut extraire que la première valeur
`var = (foo(2,1))`  
Ici var n'est pas une table.

ou bien le premier élement de la table
`tab = ({foo(2,3)})[1]`  
Ici var n'est pas une table.


# Booléen

``` lua
not true == false
(true ~= false) retourne true
```
# nil
- Veut dire la variable n'a aucune valeur asignée.

# tables 
``` lua
tab = {}
	print(x)
```
- Affiche l'adresse mémoire de la table.

``` lua
tab = {nom = "monkey", age = "25"}
	print(tab.nom)
	print(tab.age)
	print(tab[1]) --Faux car  tab est un dictionnaire
```
table 2D
: 
``` lua
tab = { data={age="25",emplois="ingenieur"}, data2={passion="retogaming",fruit="melon"} }
	print(tab.data.age)
	print(tab.data2.fruit)
```
dictionnaire
: En lua tout est une question de table 

``` lua
tab = {} -- cree une table vide 
tab[1]=1,tab[2]=2 -- cette ligne ==
tab = {1, 2} -- à cette ligne 
tab = {[1]=1,[2]=2} -- == à cete ligne
```
``` lua
tab = {} -- ces deux ligne sont les  équivalentes
tab.fruit="orange"
tab["fruit"]="orange"
```
``` lua
tab = {} -- ces deux ligne sont les  équivalentes
tab["fruit"]={} -- creer une table fruit dans tab
tab["fruit"].couleur="orange" -- creer un doctionnaire dans la table fruit
-- on peut refaire tout ça en une ligne 
tab.fruit.couleur ="orange"
```
``` lua
print(#tab) -- Donne la taille de la table 
```

> Si c'est un dictionnaire,` #tab` revoit __0__
{: .prompt-warning}

# Instructions de selection
While ..do ..end
: 

``` lua
i = 0
while i < 10 do 
	print("hello \t"..i)
	i = i + 1
end
use break to break

```

repeat .. until
: 

``` lua
i = 0
	repeat
		print("Hello World!")
		i = i+1
	until i ==10
use break  to break
```

for .i, limit, step.. do .. end
: 

``` lua
-- Numeric iteration
for i = 0,10 do 
	print("hello")
end 

-- sequential iteration
tab = {1,2, fruit = "banane", "monkey", 8}
	for key,value in pairs(tab) do
		print(key,value)
	end

	--keys : sont les index
	--value : sont les valeurs
	--fruit = "banane" est un dictionnaire donc donc index est toujours le dernier
	--donc c'est le dernier à être affiché
```
next() 
: `print(next(tab,"pi"))`  afficher la prochaie paire `(key,value)`

> `pairs()` permet d'itérer sur une paire `(key,value)`  
`ipair()` permet d'itérer que sur une paire `(index,value)`
ce qui veut dire qu'il affiche pas les dictionnaires `{nom="monkey"}`
{: .prompt-warning}

Itération avec next()
: 

``` lua
for key,value in next,tab,nil do 
	print(key,value)
end

--applique next() à tab en commençant par nil(car next nil c'est index 1) 
--la boucle for est exécuté jusqu'à ce que next() renvoit nil, nil signifie extrémité de la table
```

if .. then ..else .. end 
: 

``` lua
if.. then ..else ..end
if ..then ..elseif..then..else ..end
```
# Portée des variables 

- Pour appeler une variable d'un autres fichier : `require("nomFichier")`
- Un fichier peut retrouner une valeur en LUA si on met à sa fin `return nomVariable`
- On peut créer une variable local à un bloc : 

``` lua
if true then 
	local var = 30 -- existe que dans ce bloc 
end 
```
- une variable global est prioritaire dans le cas d'un require: 

``` lua
var = 30
require ("nomFichier") -- Il y a une variable var = 30 à l'intérieur

print(var) -- Affiche 30
```
