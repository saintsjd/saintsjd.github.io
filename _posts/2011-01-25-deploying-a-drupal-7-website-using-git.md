---
layout: post
title: Deploying a Drupal 7 Website Using git
permalink: /2011/01/deploying-a-drupal-7-website-using-git/index.html
tags: [ drupal, open source, php, git ]
---

--------------------------
### UPDATE: This post is out of date. Please see the new and improved version of this page [Automated Deployment of WordPress using git](/2011/03/automated-deployment-of-wordpress-using-git/)
--------------------------

Distributed revision control systems are an excellent way to manage the source code of web applications.  Currently I use git. In the past, I have used bzr and hg. All three great tools.

Git is now my favorite for a number of reasons.

While the tools are great at managing code and facilitating distributed development, it did take a while for me to find an efficient way to deploy my version controlled code to a live web server. The following is an outline of a simple workflow for developing and deploying web applications using distributed revision control systems. You could apply these methods to either bzr, hg, or git.

I keep development copies of the websites I manage for clients on my local machine. I develop and create new features locally on branched copies of the source code. When I complete and test a feature I commit the changes to the central code repository and deploy it to the live server using a few different methods.

### Creating your code repository
A code repository is a place to save your code. Its important to not that the repository itself is not a functioning website. It is a file often on a completely different server than the live website and the development boxes. There are two options for creating a code repository. On is github.com. I use GitHub to store my open source projects. For projects with closed source code I store my repositories on server which allows ssh connections in a folder that is not accessible via the web. You can pay for a github account which allows you to create private repositories.

**Creating an repository on Github.com**
Follow the step here: [http://help.github.com/](http://help.github.com/)

**Creating a repository on your own SSH server**
I will create my repository in a non-webaccessible directory on my live server. so... on my live server I will have:

    /home/jon/public_html (my live website folder)
    /home/jon/code (a private folder not accessible from the web to store my source code)

1. Log into the server via SSH</li>
1. my home directory is /home/jon</li>
1. I create a directory /home/jon/code for all my repositories /home/jon/code</li>
1. cd into the code directory and type:</li>

    git init --bare drupaltest.git

**Setting Up Local Development Website**

Change directories on your local development computer into a web accessible directory. On my machine the local folder /home/jon/Website is accessible at http://localhost/jon/. So I open the terminal and run:

    cd /home/jon/Website

Then:

    git clone jon@saintsjd.com:code/drupaltest.git drupaltest

This command clones the repository to my local machine and creates a localfolder called drupaltest. I can now start adding files to it. Lets unzip a fresh copy of Drupal 7 and copy all the files into the drupaltest folder. I then browse to http://localhost/jon/drupaltest/ and I run the Drupal installer.

With drupal installed we are ready to start committing our code to our git repo.

We first need to create a .gitignore file in the root of our web application /home/jon/Website/drupaltest/.gitignore. This file tells git to ignore certain files. We want to keep our settings.php file out of the repository and our sites/default/files folder out of the repository because the settings and files on our development computer will be different from the settings.php and files on the live server. My .gitignore contains:

    sites/default/files
    sites/default/private
    sites/default/settings.php

With /home/jon/Website/drupaltest/.gitignore created, I run:

    git add .
    git commit -m "initial commit"

I then send my code up to the repository on my server. Remember the repository is just a place to keep my code. It is not a live functioning website. We will create that live site later. To push the code to my respository I run

    git push origin master

And I know the command was successful when I see something like:

    Counting objects: 1099, done.
    Delta compression using up to 2 threads.
    Compressing objects: 100% (1087/1087), done.
    Writing objects: 100% (1099/1099), 2.81 MiB | 324 KiB/s, done.
    Total 1099 (delta 119), reused 0 (delta 0)
    To jon@saintsjd.com:code/drupaltest.git
    * [new branch]      master -> master

### Creating a Live Website from our Repository

The command in this section only need to be run once when you first setup your website. Like the install we ran locally we now need to run an Drupal install on our server to setup a separate database for live data. I ssh to my server and change directories into my public_html folder.

My code repo is stored on the same server as my live website but is in a folder that is not accessible from the web. Remember that i have:

    /home/jon/public_html (my live website folder)
    /home/jon/code (a private folder not accessible from the web to store my source code)

I SSH to the server and run:

    cd public_html
    git clone /home/jon/code/drupaltest.git drupaltest

If your repository is on github or on another server you will need to adjust the “/home/jon/code/drupaltest.git” path in the command above.

Then I:

    cd drupaltest
    ls

I see all my files listed. Now I browse to my website http://www.saintsjd.com/drupaltest/ and run the drupal installer.

With a working site at http://www.saintsjd.com/drupaltest/ we are ready to begin making our changes to the website and deploying the changes to the live site using git. The steps that follow are the steps you will repeat for each new feature that you want to add to your website.

###Deploying using git

There are many automated ways of deploying code using git. I recommend <a href="http://docs.fabfile.org/0.9.3/" target="_blank">Fabric</a> and <a href="http://www.heroku.com" target="_blank">Heroku</a> for Rails apps. I will show only one basic manual deployment method here.

Our workflow will be:

1. Develop and test a new features for our website on our local development box
1. Commit and push the new code to the repository
1. Pull the tested code from the repository to the live website

We repeat this workflow each time we add a new feature to our website.

Lets say I want to add the pathauto module to my drupaltest website. I download the module from http://drupal.org/project/pathauto and the token module http://drupal.org/project/token, unzip, and copy the modules to my sites/all/modules folder on my local development machine.

I browse to http://localhost/jon/drupaltest/#overlay=%3Fq%3Dadmin%252Fmodules and enable the module. I configure it and test that it actually works.

Once I am happy that the module works well. I use git to commit the new code to my repository.

    git add . (stages our new code)
    git commit -m “adding pathauto module” (commit our new code to the local repo)
    git push origin master (send the code to our origin repo on the server via ssh)

I can then SSH to my live server and cd into the public_html/drupaltest/ folder. Synchronizing my new code changes is as easy as:

    git pull

After I pull in the new code I need to enable and configure the module on the live site going to:

http://www.saintsjd.com/drupaltest/#overlay=admin/modules

Repeat the steps in the “Deploying with git” section each time you want to add a new feature to your website.

### Important! Protect your .git folder on the live website using .htaccess

I don't really want others reading my git repository on my live site by browsing to http://saintsjd.com/drupaltest/.git. I prevent this by running:

    chmod -R og-rx /home/public_html/drupaltest/.git

I also add a .htaccess file to the .git folder containing:

    Deny from all
