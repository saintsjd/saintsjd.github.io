---
layout: post
title: Simple Postgresql Database Connections with Python
tags: [ development, opensource, python ]
---

Deep in the SQLAlchemy docs, I found this gem: a simple way of querying a database from Python that returns query results as a python dictionary.

First, setup a virtualenv for your project and install SQLAlchemy and psycopg2

~~~
cd my_app
virtualenv venv
. venv/bin/activate
pip install SQlAlchemy
pip install psycopg2
~~~

Note: To install psycopg2 on my Mac I had to first install [Postgres.app](http://postgresapp.com/) and set my PATH in .bash_profile to ```PATH="/Applications/Postgres.app/Contents/Versions/9.3/bin:$PATH"```

Note: If you are on Ubuntu you will need to ```sudo apt-get install libpq-dev python-dev``` before running ```pip install psycopg2```

Now, with the virtualenv installed, you can run queries from your python scripts. Results are returned as python dictionaries:

~~~python
engine = create_engine('postgresql://scott:tiger@localhost/mydatabase')

result = engine.execute("select username from users where first_name=%s", ('Jon',) )
for row in result:
    print "username:", row['username']
    print "email:", row['email']
    print "First Name:", row['first_name']
~~~

