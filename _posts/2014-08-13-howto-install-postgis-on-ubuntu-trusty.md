---
layout: post
title: Howto install Postgresql and PostGIS on Ubuntu Trusty 14.04 with secure tunnel for connections
tags: [ development, geo ]
---

## Install Ubuntu 14.04

Start with a fresh install of Ubuntu 14.04 Trusty. (AWS or vagrant might be a good way.)

## Install Postgresql

On the server run the commands below. 

{% highlight bash %}
sudo apt-get update
sudo apt-get install -y postgresql postgresql-contrib
{% endhighlight %}

## Create a database and a user to access it

I prefer to create one user per database for security.

Replace DATABASE_NAME_HERE and USER_NAME_HERE with the values that you want to use

{% highlight bash %}
# this will prompt you for a database password
sudo -u postgres createuser -P USER_NAME_HERE
sudo -u postgres createdb -O USER_NAME_HERE DATABASE_NAME_HERE
{% endhighlight %}


## Test connecting to Postgresql

{% highlight bash %}
psql -h localhost -U USER_NAME_HERE DATABASE_NAME_HERE
{% endhighlight %}

Postgresql will ask you for your password. Then you should see:

{% highlight bash %}
psql (9.3.5)
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256)
Type "help" for help.

DATABASE_NAME_HERE=>
{% endhighlight %}

To exit type:

{% highlight bash %}
\q
{% endhighlight %}

## Optional: Add PostGIS support to the database

{% highlight bash %}
sudo apt-get install -y postgis postgresql-9.3-postgis-2.1
sudo -u postgres psql -c "CREATE EXTENSION postgis; CREATE EXTENSION postgis_topology;" DATABASE_NAME_HERE
{% endhighlight %}

## Connect to the database securely from PgAdmin3

Now, on your desktop computer install pgAdmin3 and connect securely to the database via an SSH tunnel.

First, open a terminal on your Desktop computer (these instructions are for Mac or Linux)

Open a tunnel using an ssh password or PEM file. You will need to know the username you use to SSH to the server and the server address or ip address. 

{% highlight bash %}
ssh -L 127.0.0.1:5433:127.0.0.1:5432 SSH_USERNAME_HERE@SERVER_HOST_ADDRESS -N
{% endhighlight %}

If you are using a PEM file, then you can add -i option to the SSH command

{% highlight bash %}
-i PEM_FILE_PATH_HERE.pem
{% endhighlight %}

This will open a secure tunnel between your desktop and the database server. To exit the tunnel, just press Cntrl+C.

Next, open pgAdmin3 and create a new connection using these settings:

{% highlight bash %}
Name: (describe what this connection is for here)
Host: 127.0.0.1
Port: 5433 
Username: USERNAME_HERE
Password: PASS_WORD_HERE
{% endhighlight %}

When you are done using the database close PgAdmin3 and in the terminal where the tunnel is running press Cntrl+c to exit the tunnel.
