---
layout: post
title: What is a bare git repository?
permalink: /2011/01/what-is-a-bare-git-repository/index.html
tags: [ git, open source ]
---

UPDATED: 5/30/2015 - Fixed inaccurate description of where files are stored in bare git repositories and updated explanations of both bare and no-bare repositories

**What is the difference between a repository created using the `git init` command and the `git init --bare` command?**

Repositories created with the `git init` command are called working directories. In the top level folder of the repository you will find two things:

1. A .git subfolder with all the git related revision history of your repo
2. A working tree, or checked out copies of your project files.

Repositories created with `git init --bare` are called bare repos. They are structured a bit differently from working directories. First off, they contain no working or checked out copy of your source files. And second, bare repos store git revision history of your repo in the root folder of your repository instead of in a .git subfolder. Note... bare repositories are customarily given a `.git` extension.

**Why use one or the other?**

Well, a working repository created with `git init` is for... **working**. It is where you will actually edit, add and delete files and `git commit` to save your changes. If you are starting a project in a folder on your dev machine where you will add, edit and delete files of your project, use "git init". Note: if you `git clone` a repository you will be given a **working** repository with the .git folder and copies of the working files for editing.

A bare repository created with `git init --bare` is for... **sharing**. If you are collaborating with a team of developers, and need a place to share changes to a repo, then you will want to create a bare repository in centralized place where all users can push their changes (often the easy choice is github.com). Because git is a distributed version control system, no one will directly edit files in the shared centralized repository. Instead developers will clone the shared bare repo, make changes locally in their working copies of the repo, then push back to the shared bare repo to make their changes available to other users.

Because no one ever makes edits directly to files in the shared bare repo, a working tree is not needed. In fact the working tree would just get in way and cause conflicts as users push code to the repository. This is why bare repositories exist and have no working tree.

**To summarize**

I use a working directory created with `git init` or `git clone` when I want to add, edit and delete files in `myproject` locally on my dev machine.

When I am ready, I share my local changes with a `git push` to a bare repository `myproject.git` (usually on a remote server like github.com) so other developers can access my local changes.  
