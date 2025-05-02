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
Coding > Adding code > Code is in STAGING AREA > Commit code > Push to GitHub
```

- `git init`
- `git status`
- `git add .` or `git add main.py`
- `git log`: commit ID, author, date
 - `git log --oneline`
- `git commit -m "message" -m "additional notes"` atomic changes

Each commits are linked to the previous commit.
Check HEAD to show where the code is pointing.

- `git config --global`: user.name, user.email
- `git branch branch-name`
 - `git switch -c branch-name`
 - `git checkout -d branch-name`
- `git checkout dev-branch` or `git switch`

Merging dev files to main
- `git checkout main`
- `git merge dev`

- `git branch -d dev`: Deleting the branch

- `git diff`: diff between staged and unstaged of the same files or between commits or between branch of the same file
 - you need to read plus and minus symbols with the direction of files in mind
 - not alway plus means adding code

## Hooks folder
Managing action triggers before after push or commit ecc.

