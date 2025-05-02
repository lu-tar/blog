---
title: Git, the information manager from hell
date: 2023-09-07T23:38:11+02:00
draft: false
tags:
  - cmd
  - command
categories:
  - cheat sheet
cover:
  image: img/pipes-smaller.jpg
  alt: ""
  caption: ""
ShowToc: true
author:
  - Luca
---
# Git

From Wikipedia - The source code for Git refers to the program as "the information manager from hell". (in fact, "Git tzu Gehinom" means "go to hell" in Yiddish).

## Summary

This is a collection of notes to track the logic behind code versioning in general and git. Not a trusful guide.

## Links


## .gitconfig


## Actions
```
Coding > Adding > | STAGING AREA | > Commit > Push > GitHub
```

- `git init`
- `git status`
- `git add .` or `git add main.py`
- `git log` : commit ID, author, date
 - `git log --oneline`
- `git commit -m "message" -m "additional notes"` atomic changes

Each commits are linked to the previous commit.

- `git config --global` : user.name, user.email

## Hooks folder
Managing action triggers before after push or commit ecc.

