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
- [learngitbranching.js.org](https://learngitbranching.js.org/)

## .gitconfig
Work in progress..

## Actions
```text
Coding > Adding code > Code is in STAGING AREA > Commit code > Push to GitHub
```

Remember that the HEAD is the pointer.

- `git init`
- `git status`
- `git add .` or `git add main.py`

- `git log`: commit ID, author, date
  - `git log --oneline`: lookup the hash ID

### Commit
- `git commit -m "message" -m "additional notes"` atomic changes
  - You can't switch branches without commits
  - Check the stashing of files
  - `git restore`: undo a commit
- `git commit -am "message`
- ` git commit --amend -m "git notes, pull code`change the name of the last commit message

Each commits are linked to the previous commit.
Check HEAD to show where the code is pointing.

- `git config --global`: user.name, user.email

### Branching
- `git branch branch-name`: creating new branch
  - `git switch -c branch-name`: build a branch and move to it
  - `git checkout -d branch-name`: build a branch and move to it
- `git checkout dev-branch` or `git switch`
You can move stashed code between branches, check `git stash`
- `git branch -M newname`: renaming branch


### Checkout
- `git checkout [ hash ]`: move between commits (use hash id) or branches
- Moving back to the previous commit using `git reflog` or `git checkout branch-name`
- `git log --oneline` to check the hash

### Merging dev files to main
- `git checkout main`: not only used for merging, you can move between commits with checkout
- `git merge dev`
- `git branch -d dev`: Deleting the branch
- After branching is usual to do a `commit`


### Diff
- `git diff`: diff between staged and unstaged of the same files or between commits or between branch of the same file
  - you need to read plus and minus symbols with the direction of files in mind
  - not alway plus means adding code
  - `git diff --staged`: diff staged and unstaged files
  - `git diff [commitid] [commitid]` or `git diff [commitid]..[commitid]`: diff between commits

### Stashing 
- `git stash`: temporary way to save changes
  - `git stash pop`: bring up stashed code
  - `git stash list`
  - `git stash apply`: to apply a specific stash, use stash list to see the id

- `git restore`: go back of a commit to undo the current changes

### Rebase
Powerful merge tool. Be aware. `git rebase` takes the branch and attach it to the last commit of the base branch.

```text
main ---- M1 ---- M2 ---- M3
          \____ bugfix ____ B1

After rebase

main ---- M1 ---- M2 ---- M3 ---- B1
```

Only use it from the side branch like a bug fix. After you checkout in side branch then you can go with: `git rebase master`


### Remote repository

- `git remote` - Shows configured remotes.
- `git remote -v` - Shows remotes with their respective URLs.

- `git remote add <name> <url>` - Adds a new remote.
  - `origin` name of remote repository
- `git remote rename <old> <new>` - Renames a remote.
- `git remote remove <name>` - Removes a remote.
- `git remote set-url <name> <new-url>` - Changes the URL of a remote.

- `git remote show <name>` - Details about a remote.
- `git remote prune <name>` - Removes obsolete references.
- `git remote update` - Updates all configured remotes.

#### Pushing code

- `git push -u origin main` - pushing a branch to the remote branch origin saving origin as a upstream of main.
  - `git push origin main` - without the `-u` you are not setting the upstream branch and the next push will fail 
  - `git push --set-upstream origin main`

#### Pull code
- `git pull`: download code and information
- `git fetch`: download only information

## Hooks folder
Managing action triggers before after push or commit ecc.

