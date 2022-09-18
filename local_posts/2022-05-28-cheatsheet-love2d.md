---
title: Cheat sheet Love2D
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-05-28 20:50:00 +0100
categories: [Love2D]
tags: [2d jeux]
---



# Love2D introduction
```lua
function creatReact(ax,ay)  -- Creer un rectangle, 
							-- La fonction doit avoir des paramétres 
	rect ={}
	rect.x = ax
	rect.y = ay
	rect.width = 100
	rect.height = 50
	rect.speed = 100 -- Controle de vitesse de déffilement

table.insert(list0Rectangls,rect)
end 


function love.load() -- Fonction exécutée une seule fois 

list0Rectangls = {}

creatReact(10,150)
creatReact(220,150)

end


function love.update(dt) -- Fonction exécutée en alternance avec love.draw()
	for i,v in ipairs(list0Rectangls) do 
		v.x = v.x  + v.speed * dt 
		-- Vitesse de déffilement controlé ici
		-- dt : est un delta time, dt=1/fps du Pc
		-- en 1s, mon Pc(100fps) se met à jour 100 fois en 1 seconde, chaque fois, il augmente x de v.speed*dt
		-- explication:
		-- 1/100 veut dire que chaque image prend 0.01 s 
		-- avant, je  deplace le rectangle sur l'ax X de 5 chaque 0.01s , ce qui est rapide (5pixel/0.01s)
		-- avec dt, je déplace le rectangle sur X de 0.05 chaque 0.01 s , ce qui lent (5pixel/1s)
		-- Pour augmenter la vitesse => augmenter v.speed
	end 

end 
--function love.keypressed(key)
--	if key == "space" then 
--		creatReact()
--	end

--end 

function love.draw()

	for i,v in ipairs(list0Rectangls) do -- Parcour toutes la table et leurs sous-tables et pour chaque sous-table
										-- dessiner un rectangle et le déplacer grace à update
		love.graphics.rectangle("fill", v.x, v.y, v.width, v.height)
	end

	--love.timer.sleep(1)
end 
```

# Fichier multiples et porté des variables 

