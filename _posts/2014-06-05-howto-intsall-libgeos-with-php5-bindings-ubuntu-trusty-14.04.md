---
layout: post
title: Howto install libgeos with PHP5 bindings on Ubuntu Trusty 14.04 LTS
tags: [ development, geo ]
---

Recently I needed to install libgeos with PHP5 bindings on Ubuntu 14.04 LTS. The PHP5 bindings are not available as a package in Ubuntu. Here is how I installed it from source.

{% highlight bash %}
apt-get install -y apache2 php5 libapache2-mod-php5 php5-dev phpunit

wget http://download.osgeo.org/geos/geos-3.4.2.tar.bz2
tar -xjvf geos-3.4.2.tar.bz2
cd geos-3.4.2/
./configure --enable-php
make
make install

cat > /etc/php5/mods-available/geos.ini << EOF
; configuration for php geos module
; priority=50
extension=geos.so
EOF

php5enmod geos
service apache2 restart

# note: you might need to run ldconfig here if you see permissions issues. I don't see it on my setup, but users in the comments have
ldconfig
{% endhighlight %}

You can test this in a test.php file with

{% highlight php %}
<?php

echo GEOSVersion();
{% endhighlight %}
