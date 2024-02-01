---
title: CheatSheet git
author:
  name: Aliliche larbi
  link: https://github.com/Aliliche
date: 2022-06-02 09:13:00 +0100
categories: [CheatSheet, Git]
---

#  Nouveau repo 

```shell
git init
git add files
git commit files 
git remote add origin "github-repository-url"

# show origin 
git remote -v 
# change URL 
git remote set-url origin "new url"

# push 
git push -u origin master 

```

# git stash

Permet de sauvegarder une copie de  travail localement si on changer faire quelque chose d'autre

``` shell
git stash # sauvegarder
git stash pop # restaurer
git stash apply # applique le stash sur un copy de travail ou une branche
```

Cette command est intéressante, notamment le `apply` car il permet d'appliquer les changements sur une branche
sans passer par un commit.  
# suppimer des branches 
``` shell
git branch -d <branch_name> # remove local branch
git branch -D <branch_name> # local force even not merged
git push <remote_name> --delete <branch_name> # remote branch
```
