---
layout: post
title: Simple Postgresql Database Connections with Python
tags: [ development, opensource, python ]
---

Hello wworld.... 

Deep in the SQLAlchemy docs, I found this gem: a simple way of querying a database from Python that returns query results as a python dictionary. 

First, setup a virtualenv for your project and install SQLAlchemy and psycopg2

~~~
cd my_app
virtualenv venv
. venv/bin/activate
pip install SQlAlchemy
pip install psycopg2
~~~

Now, with just a few lines you can run queries from your python scripts. Results are returned as python dictionaries:

~~~python
from sqlalchemy import create_engine
engine = create_engine('postgresql://scott:tiger@localhost/mydatabase')

result = engine.execute("select * from users where fname=%s", ('Jon',) )
for row in result:
    print "username:", row['username']
    print "email:", row['email']
    print "First Name:", row['fname']
~~~

There are a few advantages to doing things this way:

1. Easier syntax than the raw psycopg2 driver
1. Uses SQLAlchemy connection pooling by default
1. It appears that SQLAlchemy does the right thing and closes connections if an exception is thrown or the script exits successfully.

Note: To install psycopg2 on my Mac I had to first install [Postgres.app](http://postgresapp.com/) and set my PATH in .bash_profile to ```PATH="/Applications/Postgres.app/Contents/Versions/9.3/bin:$PATH"```

Note: If you are on Ubuntu you will need to ```sudo apt-get install libpq-dev python-dev``` before running ```pip install psycopg2```
