---
title: "Note-taking with Obsidian"
date: 2022-05-28T17:04:55+02:00
draft: false
tags: ["obsidian", "markdown", "note-taking", "hypertext"]
categories: ["Productivity"]
cover:
	image: img/obsidian-logo.png
ShowToc: true
author: ["Luca"]
---
# Note-taking with Obsidian
In this article Hugo + Netlify + GitHub I talked about how I setup this website with my few programming skills. In those lines I mentioned frequently markdown files and Obsidian so in this article I'm going to explain how I organize and take notes. But first let's address some questions

### What is Obsidian? 
[Obsidian](https://obsidian.md/) is a free, open-source note-taking app which helps you link your notes to each other using hypertext with markdown files.

### Why I need to switch from OneNote, Google Drive or Evernote to Obsidian?
You don't. You don't especially because Obsidian is a pain in the ass to configure and mantain a well-ordered list of notes, both useful and practical and cool-at-the-eyes, is not a simple task and I'm far from achiving it.

Although, I find this app very useful because with a keyboard shortcut I can open a new notes with a specific template like "Troubleshooting Cisco switch stack", link on the fly an old note called "Cisco command cheatsheet" or search all the notes tagged with "9300".

### Is Obsidian a collaborative app?
Obsidian is not a native collaborative app but maybe there are some plugins and workaround to the lack of collaboration.

### Are the notes automatically sync to a cloud?
No, the files are store locally in your machine and even with the paid functionality [Sync](https://obsidian.md/sync) there is no Obsidian cloud.

## First setup
These are my mandatory steps to do before start using the application:

- Go to Settings
	- Options
		- Editor
			- Turn on **live preview** Editor -> under General you must disable Legacy Mode. Set Default editing mode to "Live Preview".
			- If you don't want a **centered text** go to Settings -> Editor -> under Display -> Readable line lenght
			- Turn on **fold heanding** and **fold indent** to collapse text under headings or lists
		- File and links
			- Setup the **new notes default folder**
			- Setup the **attachment folder**
		- Core plugins
			- Activate the **templates plugin**
		- Hotkeys
			- Set the `Toggle code` shortcut to `CTRL + SHIFT + <`
			- Set the Template shortcut `CTRL + SHIFT + I`

That's a lot :skull: but we are almost done.

## Folders, tags and links
### Folders
Once we finished the first setup we can start working with Obsidian keeping in mind that is a tool based on hypertext or "now I can link together two or more files", so for this reason I like to have a few folders and create a logical structure using links as much as possible.

My goto base folder structure is the following:

```
My Vault
	Archive
	Attachments
	KB
	Templates
```

- **Archive**: where i store the notes of finished activities or tickets.
- **Attachments**: screenshots are stored in this folder.
- **KB**: this is the folder dedicated to my knowledge base where I put summaries of official guides or community posts with the links to each sources. If I find a note particulary interesting or well done outside of KB I will move that note in that folder.

### Tags
Tags are self explanatory, you can use them to mark a note with a category like "multicast" or "BGP" to summarize in a few words the content of the note.

### Links
Links are the core of Obsidian and the syntax is the following

- Link to a note `![[Note name]]` or `[[Note name]]`
- Link to an external page `[Link to Wikipedia](https://wikipedia.org)`

With `!` before the name I'm displaying the content of the link directly in the note I'm currently working on.

## Templates
Templates will save you a lot of time putting in place a blueprint of a note with very few steps so you don't need to rewrite anything.


## How I link together notes in Obsidian


### Reference
1. [Zettelkasten in Obsidian](https://youtu.be/E6ySG7xYgjY?t=137)
2. [Obsidian for beginners](https://www.youtube.com/watch?v=QgbLb6QCK88)
3. [Markdown Cheat Sheet](https://www.markdownguide.org/cheat-sheet/)






