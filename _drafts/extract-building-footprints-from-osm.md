---
layout: post
title: How to extract building footprints from OSM
tags:
---

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
~~~
