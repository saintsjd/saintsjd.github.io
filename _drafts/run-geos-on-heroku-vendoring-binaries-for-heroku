---
layout: post
title: Run Vendored Binaries on Heroku
tags: heroku, devops, deployment, joy
---
Recently I needed to install the python [Shapely](https://pypi.python.org/pypi/Shapely) library on an app on Heroku. Shapely has a dependancy on the C Library [GEOS](http://trac.osgeo.org/geos/) which is not part of the base Heroku system. 

Here is how I complied and vendored the GEOS binary so I could run shapely on Heroku. You can use this same process to vendor and run any binary dependancy that compiles on Linux on the Heroku platform. 

Here are links which helped me figure all of this out:

* http://www.petekeen.net/introduction-to-heroku-buildpacks
* https://github.com/ddollar/heroku-buildpack-multi
* https://github.com/peterkeen/heroku-buildpack-vendorbinaries
