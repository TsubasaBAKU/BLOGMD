---
title: GitCommand
date: 2016-05-25 17:18:38
tags:
	- Git
categories:
	- GIT
---
## GIT BASH常用命令总结
<!--more-->
```git
$ git remote add origin url
$ git config --global user.name
$ git config --global user.email
$ git confing -l
$ ssh-keygen -t rsa -C "email"
$ git remote add origin url
$ git push -u origin master
$ git push origin master
$ git clone rul
$ git branch brh
$ git branch
$ git checkout brh
$ git checkout -b brh
$ git branch -d brh
$ git remote set-url origin url
$ git push origin master
$ git push origin brh
$ git merge brh（on master）
$ git branch -d brh
$ git push origin --delete brh
$ git branch -m 520 lsy
$ git branch -a
$ git branch -r
$ git branch -l
$ git branch -D lsy
$ git push origin :lsy
$ git log --graph --pretty=oneline
$ git merge --no-ff -m "no ff" lsy
$ git log --graph --pretty=oneline --abbrev-commit
$ git add .
$ git stash
$ git stash list
$ git stash apply
$ git stash drop
$ git stash pop
$ git diff master > MyPatcher
$ git apply MyPatcher
$ git format-patch -M master
$ git am 0001-lsy-commit.patch
$ git merge origin/brh
$ git branch --set-upstream-to=origin/brh
$ git fetch 
$ git pull
$ git tag v1.0
$ git tag 
$ git log --pretty=oneline --abbrev-commit
$ git tag v0.8 76a68ef
$ git show v0.8
$ git tag v0.1 -m "first version" cb12c24
github.com/github/gitignore
.gitignore
$ git add .gitignore
$ git commit

```
