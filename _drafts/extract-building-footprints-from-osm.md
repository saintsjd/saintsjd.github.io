---
layout: post
title: How to extract building footprints from OSM
tags:
---

Amazon ec2 instance with 64GB ram. Takes about 1 hour.

~~~
buildings.style
# OsmType  Tag          DataType     Flags
node,way   building     text         polygon

ubuntu@ip-172-30-1-100:~$ createdb africa
# run postgis init commands
# -C is 75% of memory 48000 on 64GB machine
ubuntu@ip-172-30-1-100:~$ osm2pgsql -c -d africa --slim -C 48000 --flat-nodes flat-nodes -S ./buildings.style africa-latest.osm.pbf
pgsql2shp tanzania planet_osm_polygon

sudo apt-get update
sudo apt-get upgrade
sudo apt-get install postgresql postgresql-contrib osm2pgsql
sudo -i -u postgres
psql
sudo apt-get install -y postgis postgresql-9.3-postgis-2.1
sudo -u postgres psql -c "CREATE EXTENSION postgis; CREATE EXTENSION postgis_top
ology;" ubuntu


sudo apt-get update
sudo apt-get upgrade
sudo apt-get install postgresql postgresql-contrib osm2pgsql
sudo -i -u postgres
psql
sudo apt-get install -y postgis postgresql-9.3-postgis-2.1
sudo -u postgres psql -c "CREATE EXTENSION postgis; CREATE EXTENSION postgis_top
ology;" ubuntu
psql
more .bash_history 
exit
wget http://download.geofabrik.de/africa-latest.osm.pbf
nano buildings.style
wget http://download.geofabrik.de/africa/tanzania-latest.osm.pbf
createdb tanzania
psql tanzania
nano -w buildings.style
top
osm2pgsql -c -d tanzania --slim -C 48000 --flat-nodes flat-nodes tanzania-latest
.osm.pbf 
osm2pgsql -c -d tanzania --slim -C 48000 --flat-nodes flat-nodes -S ./buildings.
style tanzania-latest.osm.pbf 
pgsql2shp 
psql tanzania 
pgsql2shp 
psql tanzania 
mkdir tanzania
cd tanzania/
pgsql2shp tanzania planet_osm_polygon
ls
cd ../
tar -czvf ./tanzania.tar.gz ./tanzania
createdb africa
osm2pgsql -c -d africa --slim -C 48000 --flat-nodes flat-nodes -S ./buildings.st
yle africa-latest.osm.pbf 
psql africa 
osm2pgsql -c -d africa --slim -C 48000 --flat-nodes flat-nodes -S ./buildings.st
yle africa-latest.osm.pbf 
exit
~~~
