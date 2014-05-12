---
layout: post
title: Run Vendored Binaries on Heroku
tags: heroku, devops, deployment, joy
---

You can use the steps below to vendor and run binary dependencies on the Heroku platform. 

Recently I needed to install the python [Shapely](https://pypi.python.org/pypi/Shapely) library on an app on Heroku. Shapely has a dependency on the C Library [GEOS](http://trac.osgeo.org/geos/) which is not part of the base Heroku system. 

Here is how I complied and vendored the GEOS binary for Heroku. You could use the same process to vendor a different binary.

Links that helped me figure all of this out:

* http://www.petekeen.net/introduction-to-heroku-buildpacks
* https://github.com/ddollar/heroku-buildpack-multi
* https://github.com/peterkeen/heroku-buildpack-vendorbinaries

Step #1: Setup your git repository and heroku app locally
----

I assume you have read and followed the appropriate Heroku Getting Started Guide for your language. I used [Getting Started with Python on Heroku](https://devcenter.heroku.com/articles/getting-started-with-python)


Step #2: Compile binaries on Heroku
----

With your application configured, run bash on a Heroku process

```bash
heroku run /bin/bash
```

From the Heroku bash command prompt, download and extract the source code for the binary package you need to run on Heroku. In my case it was:

```shell
curl -O http://download.osgeo.org/geos/geos-3.4.2.tar.bz2
tar -xjvf geos-3.4.2.tar.bz2
```

Open the README or the INSTALL doc for your source code and follow the compile instructions with one change. Change the `prefix` for the install to be something easy to find in your /app folder.

```shell
cd geos-3.4.2
mkdir /app/scratch-space
./configure --prefix=/app/scratch-space
make
make install
```

When the compiling is finished you will have a working binary in the /app/scratch-space folder. In my case geos made three folders in the scratch-space folder: `bin`, `lib`, and `include`.

Create an zip archive of the binaries. 

```shell
cd /app/scratch-space
tar -czvf /app/geos-3.4.2-heroku.tar.gz .
```

Step #3: Add the vendor library to your local source control 
----

Next copy the geos-3.4.2-heroku.tar.gz to your local machine. I did this in two steps. You might have a better method. I used `scp` to copy the file to another server and then `scp` again copy the geos-3.4.2-heroku.tar.gz to my local machine. 

With the geos-3.4.2-heroku.tar.gz on your local machine extract it into a folder .heroku/vendor in the root of your app. 


```shell
# from my app root folder
mkdir .heroku
mkdir .heroku/vendor
cd .heroku/vendor
tar -xzvf ~/Downloads/geos-3.4.2-heroku.tar.gz
```

Make sure you have the same folder structure as scratch-space in .heroku/vendor. In my case I have three folders: `bin`, `lib`, and `include`.

```shell
ls .heroku/vendor
bin include lib
```

Add the binaries to your git repo and deploy to Heroku. The binaries will be available to your app.

```shell
git add .
git commit -m "vendored binary for lib geos"
git push heroku master
```

Optional Step #4: Use buildpacks instead of adding binaries to local git repo
----

Instead of Step #3 you can also use the Multi Buildpack and the Vendored Libraries build if you prefer not to have vendored binaries in your git repo. 

Copy your binary from Step #2 to amazon s3. 

Install Multi build pack support. See https://github.com/ddollar/heroku-buildpack-multi

Install Vendored Binary buildpack support and create a .vendor_urls file https://github.com/peterkeen/heroku-buildpack-vendorbinaries

