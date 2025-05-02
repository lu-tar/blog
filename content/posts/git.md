---
title: Git, the information manager from hell
date: 2025-02-05T13:14:00+02:00
draft: false
tags:
  - cmd
  - command
  - git
categories:
  - cheat sheet
  - notes
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

Work in progress..

## .gitconfig
Work in progress..

## Actions
```text
Coding > Adding code > Code is in STAGING AREA > Commit code > Push to GitHub
```

- `git init`
- `git status`
- `git add .` or `git add main.py`
- `git log`: commit ID, author, date
 - `git log --oneline`
- `git commit -m "message" -m "additional notes"` atomic changes
 - You can't switch branches without commits
 - Check the stashing of files

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

 - `git diff --staged`: diff staged and unstaged files
 - `git diff [commitid] [commitid]` or `git diff [commitid]..[commitid]`: diff between commits
 
- `git stash`

## Hooks folder
Managing action triggers before after push or commit ecc.

