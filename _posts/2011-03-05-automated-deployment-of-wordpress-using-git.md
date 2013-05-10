---
layout: post
title: Automated Deployment of PHP Applications using git 
permalink: /2011/03/automated-deployment-of-wordpress-using-git/index.html
tags: [ php, git, automatic ]
---

[Heroku](http://www.heroku.com) is a sweet hosting platform which offers a pure git deployment workflow. I love using it to deploy my Rails projects. I miss using it when I deploy a PHP application.

Lets fix that.... Here is a quick heroku-ish git deployment workflow for your PHP applications. We will be using Wordpress for this example. The same steps can be adapted for Drupal or any other PHP application.

Our goal is to development and test new features, themes, and plugins for Wordpress locally on our development machine. When changes are working properly, we want to deploy them to our live site with one command:

    git push origin master

### #1: Set up password-less login to your webserver with SSH keys

To run deployments we will need to access our live webserver without a password. Follow the steps  in [Setting up ssh public keys](http://www.saintsjd.com/2011/01/setting-up-ssh-public-keys/) to login to webserver without a password.

For each user or machine from which you want to allow deploys you will need to install its SSH key using the steps above.

### #2: Setup Code Repository on the Webserver

I connect to the webserver via SSH. My home directory is /home/saintsjd.

I have a /home/saintsjd/www folder for my website and PHP files. I also have a /home/saintsjd/code folder for my git repositories.

cd into /home/saintsjd/code and create a folder called blog.git

	cd /home/saintsjd/code
	mkdir blog.git

Next, initialize a bare git repository

	cd blog.git
	git init --bare blog.git

This gives me a git repository to store my code at /home/saintsjd/code/blog.git. I then disconnect from the SSH connection to the webserver.

### #3: Setup Development Box

I have php, mysql, and apache running locally on my laptop. I create my Wordpress themes, plugins, and updates on my laptop. After I test them, I deploy them to the live webserver for the world to see.

My local website folder is /home/jon/Website. To get started I clone my git repo locally:

	git clone ssh://saintsjd@saintsjd.com/home/saintsjd/code/blog.git ./blogdev

This give me:

	/home/jon/Website/blogdev

Now i can start filling in code!

Into the /home/jon/Website/blogdev/ folder I copy in all the files for Wordpress and run the installer.

Next, I need to add a gitignore file to the project. I use the one here. https://github.com/github/gitignore/blob/master/Wordpress.gitignore. I copy the contents of that file into a .gitignore file in /home/jon/Website/blogdev/.gitignore. I then:

	git add .
	git commit -m "inital commit"</pre>

To send my changes to the server I type:
	
	git push origin master

### #4: Setup Live Webserver

Now I install Wordpress on my server. I SSH to my server. There my website directory is /home/saintsjd/www. I need to remove this folder so I backup everything in this directory.  Then I remove it and recreate it using git clone. I type:

	rm -rf /home/saintsjd/www
	git clone /home/saintsjd/code/blog.git /home/saintsjd/www

I then browse to my website http://www.saintsjd.com and run the wordpress installer so the database gets installed on the server.

### IMPORTANT: Protect your .git repository from prying eyes

I don't really want others reading my source code from the git repository via http://www.saintsjd.com/.git/

I prevent this by running:

	chmod -R og-rx /home/saintsjd/www/.git

I also add a .htaccess file to the .git folder containing:
	Deny from all

### #5: Automated Deployment Script

Now that wordpress is installed on both my local machine and my live webserver, I want to be able to deploy any changes I make on my local copy with one git push command. I can do this using a Git hook.

I SSH to my webserver and create a file /home/saintsjd/code/blog.git/hooks/post-receive

	touch /home/saintsjd/code/blog.git/hooks/post-receive

I make the file executable

	chmod u+x /home/saintsjd/code/blog.git/hooks/post-receive

Then I edit the file

	nano -w /home/saintsjd/code/blog.git/hooks/post-receive

and place this code in it (adjusting the config variables to match your server settings)

	#!/bin/bash
	#CONFIG
	LIVE="/home/saintsjd/www"

	read oldrev newrev refname
	if [ $refname = "refs/heads/master" ]; then  
		echo "===== DEPLOYING TO LIVE SITE ====="  
		unset GIT_DIR
		cd $LIVE
		git pull origin master
		echo "===== DONE ====="
	fi

### #6 Let the (heroku-like) Fun Begin: Automated Deployment for Wordpress

Now that everything is setup. I am free to develop new themes, install new plugins and update on my local development box. Once everything is working properly locally commit and push. The post-receive hook automatically deploys the code changes to my live webserver for me.

Here are my steps:

1. Make changes to my local site's code: new theme, new plug-in, etc
1. test, test and test so more!
1. When I am happy I commit to the local git repository:

    git add .
	git commit -m "new theme css for header"

1. I push and deploy to the server using one command:

	git push origin master

1. After the post-receive hook moves the new code to the live website. I open http://www.saintsjd.com/wp-admin on the live server to trigger and database updates that need to run

That's it... heroku-ish git deployment for WordPress websites.