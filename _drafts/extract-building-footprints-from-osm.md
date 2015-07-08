---
layout: post
title: How to extract building footprints from OSM
tags:
---

buildings.style
# OsmType  Tag          DataType     Flags
node,way   building     text         polygon

ubuntu@ip-172-30-1-100:~$ createdb africa
# run postgis init commands
# -C is 75% of memory 48000 on 64GB machine
ubuntu@ip-172-30-1-100:~$ osm2pgsql -c -d africa --slim -C 48000 --flat-nodes flat-nodes -S ./buildings.style africa-latest.osm.pbf
pgsql2shp tanzania planet_osm_polygon
