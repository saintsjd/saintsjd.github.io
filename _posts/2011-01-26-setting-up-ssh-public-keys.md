---
layout: post
title: Setting up ssh public keys to login to SSH servers without a password 
permalink: /2011/01/setting-up-ssh-public-keys/index.html
tags: [ ssh, howto ]
---

------------------
### This post is out of date and could overwrite any existing SSH keys that you have. A better explanation comes from the kids at Github. Please read [Generating SSH Keys](https://help.github.com/articles/generating-ssh-keys) instead.
------------------

SSH public keys allow you to securely login to a remote SSH server with having to enter your password each time you want to log in.

Once configured SSH keys can save you time and enable easier use of applications like Git. Below are steps for Mac and Linux users to create SSH keys and install them on remote servers for passwordless logins.

### Create an SSH key on your local machine

From the command line type:

    ls .ssh

If you see a file called id_rsa.pub listed, then you already have created a key. Skip to the next section called "Copying the key to the server".

If you do not see id_rsa.pub listed. Lets create one. Type:

    ssh-keygen -t rsa

Setting a password for your key is recommended, but it optional. Once the process completes, run:

    ls .ssh

And verify that you have a id_rsa.pub file.

### Copying the key to the server

There are two methods for copying the keyfile to the server. Ubuntu Linux has the easiest method. A special command ssh-copy-id. Go to the command line and try (change the username and server name):

    ssh-copy-id username@servername.com

You'll be prompted to accept the identity of the server. Say "yes". Then enter your password. Once the file is copied, you are ready to login without passwords.

If your computer does not have the ssh-copy-id command you need to manually open the id_rsa.pub file in a text editor and copy its contents (this is your public key). Carefully paste the public key at the end of a file in your home directory on the server located at .ssh/authorized_keys2. If the .ssh directory and the authorized_keys2 files do not exist you can create them, but make sure permissions are set so others users cannot view the folder or files.

With your key installed you are ready to login without a password!

### Login via SSH without a password

Go to the command line and type:

    ssh username@servername.com

Your first time you may be asked to accept the identity of the server. Say "yes". If your public key is installed correctly you will be able to login to the server without a password.

Any other SSH server you want to log into with a password... just install your public key id_rsa.pub on the server using the steps above.
