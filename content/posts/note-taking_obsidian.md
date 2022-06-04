---
title: "Note-taking with Obsidian"
date: 2022-05-28T17:04:55+02:00
draft: false
tags: ["obsidian", "markdown", "note-taking", "hypertext"]
categories: ["productivity"]
cover:
 image: img/obsidian-logo.png
 alt: ''
 caption: ''
ShowToc: true
author: ["Luca"]
---
# Note-taking with Obsidian
In this article Hugo + Netlify + GitHub I talked about how I setup this website with my few programming skills. In those lines I mentioned frequently markdown files and Obsidian so in this article I'm going to explain how I organize and take notes. But first let's address some questions

### What is Obsidian? 
[Obsidian](https://obsidian.md/) is a free, open-source note-taking app which helps you link your notes to each other using hypertext with markdown files.

### Why I need to switch from OneNote, Google Drive or Evernote to Obsidian?
You don't. You don't especially because Obsidian is a pain in the ass to configure and mantain a well-ordered list of notes, both useful and practical and cool-at-the-eyes, is not a simple task and I'm far from achiving it.
[[image of a markdown file]]
Although, I find this app very useful because with a keyboard shortcut I can open a new notes with a specific template like "Troubleshooting Cisco switch stack", link on the fly an old note called "Cisco command cheatsheet" or search all the notes tagged with "9300".
[[gif of what i just sad]]

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

My Vault
	Archive
		*Pandas.md*
	Attachments
	KB
	Templates
*Dogs.md*
*Cats.md*

### Tags

### Links

## Templates
