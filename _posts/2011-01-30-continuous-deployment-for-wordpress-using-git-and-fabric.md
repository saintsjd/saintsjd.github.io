---
layout: post
title: Continuous deployment for Wordpress using git and fabric 
permalink: /2011/01/continuous-deployment-for-wordpress-using-git-and-fabric/index.html
tags: [ WordPress, open source, fabric, howto ]
---

----------------------
### UPDATE: This post is out of date. Please see the new and improved version of this page [Automated Deployment of WordPress using git](/2011/03/automated-deployment-of-wordpress-using-git/)
----------------------

[Continuous Deployment](http://radar.oreilly.com/2009/03/continuous-deployment-5-eas.html) is a strategy you'll want to implement for any successful web project. According to Eric Reis, "It’s a process whereby all code that is written for an application is immediately deployed into production. The result is a dramatic lowering of cycle time and freeing up of individual initiative. It has enabled companies I’ve worked with to deploy new code to production as often as fifty times every day."

This article describes one method for configuring a continuous deployment infrastructure using git, fabric, and SSH. Its fairly lightweight and should work with basic hosting systems.

Our goal here is to create new features locally on a development machine, commit happy changes to a code repository, and finally deploy the changes to a live web server with ease using server side scripts. It makes deploying new changes to a live wordpress website so easy you'll want to do it 50 times a day!

### Install Deployment Tools on your Dev Machine:

I use fabric for all of my non-rails applications (PHP, Python, etc). Fabric allows you to execute command on remote servers by running a local script on your machine.

Its available at http://docs.fabfile.org/

To install on my local Ubuntu Linux development machine I just:

    sudo apt-get install fabric

### Set up SSH keys 

To run deployments we will need to access our remote SSH server without a password. Follow the steps  in [Setting up ssh public keys](http://www.saintsjd.com/2011/01/setting-up-ssh-public-keys/) to login to SSH servers without a password.

For each user or machine from which you want to allow deploys you will need to install its SSH key using the steps above.

### Use git to Setup Code Repositories

I use two separate git repositories: one for my wordpress website and another to track changes to my fabric deployment scripts. I store my repositories on my web server in the folder

    /home/jon/code/saintsjd.com
    /home/jon/code/saintsjd.com/www.git (is my wordpress website git repo)
    /home/jon/code/saintsjd.com/deploy.git (is my repo for deployment scripts)

So I cd into /home/jon/code/saintsjd then:

    git init --bare www.git
    git init --bare deploy.git

### Setup a Local Development Space

I have php, mysql, and apache running locally on my development machine. My local website folder is /home/jon/Website. There I create a folder /home/jon/Website/saintsjd.com. I then clone my git repos locally:

    git clone ssh://jon@saintsjd.com/home/jon/code/saintsjd.com/deploy.git ./deploy
    git clone ssh://jon@saintsjd.com/home/jon/code/saintsjd.com/www.git ./www

This give me:
    
    /home/jon/Website/saintsjd.com/www
    /home/jon/Website/saintsjd.com/deploy

Now i can start filling in code!

### Install Wordpress in Dev Space 

Into the /home/jon/Website/saintsjd.com/www/ folder I copy in all the files for wordpress and run the wordpress installer. After WP is installed properly on my local machine I initialize a git repository to start tracking my code.

First, I need to add a gitignore file to the project. I use the one here. https://github.com/github/gitignore/blob/master/Wordpress.gitignore. I copy the contents of that file into a .gitignore file in /home/jon/Website/saintsjd.com/www/.gitignore. I then:

    git add .
    git commit -m "inital commit"

To send my changes to the server I type:

    git push origin master

### Install Wordpress on the server

Now I install wordpress on my server. I ssh to my server. There my website directory is /home/jon/public_html. I backup everything in this directory then I remove this directory and recreate it using git clone. I type:

    rm -rf public_html
    git clone /home/jon/code/saintsjd.com/www.git public_html

I then run the wordpress installer.

### Writing the deploy scripts

With wordpress installed locally and on my server. I am ready to write a fabric deploy script to easily synchronize changes I make locally to my live version of wordpress on the web server.

On my development machine I:

    cd /home/jon/Website/saintsjd.com/deploy

There I create fabfile.py and fill the file with the code below:

    from fabric.api import *
    env.hosts = ['jon@saintsjd.com']
    WEBSITE_PATH = "/home/jon/public_html"

    def deploy():
        with cd(WEBSITE_PATH):
            run('git pull')

You will need to change the env.hosts value to use your ssh login for your server. You will also need to change WEBSITE_PATH to reflect the path to your website on the server.

Now when ever we want to deploy changes to the live website we can just

1. commit our changes with git and 	push</li>
1. go to the command line and

    fab deploy

If you want to (I do), you can get fancy and make a backup before each deployment. This is great in case something goes wrong. My fabfile.py with backups looks like:

    from fabric.api import *
    
    env.hosts = ['jon@saintsjd.com']
    LIVE = "/home/jon/www/saintsjd.com"
    BACKUPS = "/home/jon/backups/saintsjd.com"
    MYSQL_USER = ""
    MYSQL_PASSWORD = ""
    MYSQL_DATABASE = ""

    def deploy():
        backup()
        with cd(LIVE):
            run('git pull origin master')

    def backup():
        run('mysqldump -u %s -p %s --add-drop-table --password=%s  &gt; %s/saintsjd.com-database-current.mysql' % (MYSQL_USER, MYSQL_DATABASE, MYSQL_PASSWORD, BACKUPS) )
        run('/usr/bin/nice tar -czf %s/saintsjd-`date +%%Y%%m%%d%%H%%M%%S`.tar.gz %s/saintsjd.com-database-current.mysql %s' % (BACKUPS,BACKUPS,LIVE) )</pre>

With fabfile.py written and working I add it to my git repo and push to the server for safe keeping:
    
    git commit -am “initial commit”
    git push origin master

### Protect your .git folder on the live website using .htaccess

I don't really want others reading my git repository via http://www.saintsjd.com/.git/ I prevent this by  running:

    chmod -R og-rx /home/public_html/.git

I also add a .htaccess file to the .git folder containing:

    Deny from all

### Continuous deployment of Wordpress using git and fabric

My continuous deployment for the website is all set. Now I

1. make changes to my local site's 	code: new theme, new plug-in, etc
1. test, test test
1. when I am happy I commit to the 	local git repository
1. I push to the server

    I then change directory into my 	../deploy folder and run

	    fab deploy

    on the command line to send the change to the live 	website</li>
1. finally I open wp-admin on the 	live server to trigger and database updates that need to run