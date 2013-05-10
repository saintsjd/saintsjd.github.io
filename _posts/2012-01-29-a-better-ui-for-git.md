---
layout: post
title: A Better UI (CLI) for git 
permalink: /2012/01/a-better-ui-for-git/index.html
tags: [ git, ui, less, friction ]
---

I recently read 3 UI books

1. Design of Everyday Things
1. About Face 
1. Don't Make Me Think.

The books did make me think. A lot. A lot about a tool I use everyday...
git <a href="http://git-scm.com" target="_blank">http://git-scm.com</a>.

Git is sweet tool. But, the UI could use some love.

The UI can be confusing and inconsistent for newbies. In fact, even after three years of using git, there are commands I still find confusing (do I want git revert or git reset... and does it need --hard, HARD, --cached, HEAD, --, or  something else?).

An interface should never be a by-product of the implementation of the system. This is where the Git UI goes wrong.

The command <strong>git reset</strong> is a good example. I use <strong>git reset</strong> to undo things. But, its called "reset" and has a bunch of strange parameters which have to do with making writes to the git index. In order to undo changes properly, the reset command requires the user to have a clear understanding of the guts of the git index. This should not be the case for a common command, like undo.
<pre># Why not replace things like: git reset HEAD -- file with:
&gt; git unstage</pre>
If we make all the "resetting" and funny "HARD -- HEAD" parameters happen behind the scenes, then we make the user happier. A lot happier.

Here are a few other small irks I have with the git UI:
<ul>
	<li>I have some files I want to stage. Do I want <strong>git add .</strong>, <strong>git add -u</strong>, or <strong>git add -A</strong>? Which does what again? Confused.</li>
	<li>Oh geez. I just staged something by accident. How do I unstage it? Its <strong>git reset</strong>? Or is it <strong>git revert</strong>? Do I do a HARD, --, HEAD, FILE, or something else? Brain damage.</li>
	<li>I want to see my changes. <strong>git diff</strong>? <strong>git diff HEAD</strong>? or <strong>git diff --cached</strong>? why not <strong>git diff CACHED</strong>? Grrrr....</li>
	<li><strong>git status</strong> is cluttered with commands telling me how to undo things "use git reset HEAD &lt;file&gt; to unstage". If the commands were easy enough to remember in the first place, we would not need to clutter the status output with reminders. And, what does "changed but not updated mean"? I did update my file! Do you mean "changed but not staged"? Lastly, I would like to humbly request a few fewer "#" characters. Just a few. Please.</li>
</ul>
Here is my shot at re-creating a few parts of the Git UI. My hope is that all of these commands become part of git one day and that git becomes easier for new users and forgetful intermediates like me.
<h2>Staging files</h2>

	# Stage all changes, and file deletions. Leave unknown files alone.
	# Instead of git add -u, just type:
	> git stage

	# Stage just one file change or deletion.
	# Instead of git add FILE and git rm FILE, just type:
	> git stage FILE

<h2>Unstaging Files</h2>
Ooops. I staged a changed that I do not want to commit.

	# Remove one file from the stage.
	# Instead of git reset HEAD -- file, just type:
	> git unstage FILE

	# unstage all the files you have in the stage right now.
	# Instead of git reset HEAD, just type:
	> git unstage

<h2>Undoing changes I have made:</h2>

Ok. I tried something. It does not work. Just get me back to the last commit.

    # Undo all the changes I made to working directory.
    # Automatically save the user's work in a stash, then
    # Instead of git stash; git reset --hard HEAD, just type:
    > git retreat
    All files changed back to their state at the last commit.
    Your work has been saved in stash@{1}. 

    # Just undo the changes I made to one file.
    # Automatically save a stash of the user's work first
    # Rather than git checkout FILE (side note: why is it not git reset FILE? or is it?)
    # Instead, just type:
    > git retreat FILE
    The file was changed back to the it's state at last commit. 
    Your work has been saved in stash@{1}.</pre>

<h2>Deleting Files</h2>
Let's get rid of git rm. Lets not even tell newbies about it. Just delete files you want from your project, then:

	# smart enough to stage files you have removed as well!!!!
	> git stage
	> git stage FILE

<h2>Status</h2>

More readable. Less # signs. Less instructions.

	> git status

	Staged for commit:
    	M daemon.c
    	N test.txt

	Unstaged Changes (will NOT be committed):
    	M hello.c
    	? new.c</pre>

Nice and clean. No reminders about how to undo things... you already know that: <strong>git undo</strong>. And, no # signs!!

<h2>Automatic Setup</h2>
The first time I run git from a new computer I am most often greeted with an error. Not a very friendly way to great new users. The message says that I have not set my email and user name.

If this error occurs, shouldn't git just ask me for this information and proceed with the command I typed? Yep, it should.

	# my first ever commit with GIT! Don't greet me with an error.
	> git commit -m "yippeee git!"

	Howdy! So that git can give you credit for changes you make
    	Please enter your name? Jon Saints
    	Please enter your email? email@gmail.com
	Thank you! Committing...

This replaces the more confusing error message that git shows users now:

	Error 123: Look! Another stupid user is _trying_ to learn git. 

	You should have known to type this first:
	> git config --global user.name "Jon Saints"
	> git config --global user.email "gmail@gmail.com"

	DUFUS!! Just give up now. Seriously.

<h2>Switching branches</h2>

	# I switch branches with the checkout command. Its ok, but what if we had:
	> git switch BRANCH

<h2>Showing things I have changed</h2>

This is one of the most important parts of git. And, it is confusing.

	# What is the output? Can you remember which is which?
	# git diff
	# git diff HEAD
	# git diff --cached or git diff CACHED?

	# Instead we can... 

	# Show all changes from the last commit
	> git diff
	# or
	> git diff HEAD

	# Show difference between the stage and my code
	> git diff STAGE

	# Difference between my stage and last commit
	> git diff STAGE HEAD

	# Difference between two branches or revisions
	> git diff branch1 branch2
	> git diff AD34E BCDE543

<h2>Deleting a branch</h2>

	# Let's be consistent with git remote command. Instead of git branch -d
	> git branch rm BRANCH

<h2>Where to from here?</h2>

I am going to make a small python wrapper to try out these commands. It will be called gum. And, it will just write git commands for you.

gum = Git User (interface) Makeover.

"gum unstage" here we come! The code is on github: <a href="https://github.com/saintsjd/gum" target="_blank">https://github.com/saintsjd/gum</a>

Comments welcome.