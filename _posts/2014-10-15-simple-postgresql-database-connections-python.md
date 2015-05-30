---
layout: post
title: Simple Postgresql Database Connections with Python
tags: [ development, opensource, python ]
---

### UPDATE:

I've spent some time using the method that I outlined below and found some problems with it. I now have a new prefered way of querying postgres from python and getting results as a dictionary. You should use the new way in most cases. 

Here are the problems with the previous method:

1. The previous method requires sqlalchemy which is a large dependency
2. The previous method was using a sqlalchemy connection pool and leaving connections open too long. This was a problem when I tried to us this code in background worker tasks that needed to control when connections are closed (so they could open a connection, read some data, disconnect, run a calculation for a while, then re-connect and write back to the database). Leaving the connections open in a pool in sqlalchemy on a large set of workers was consuming all the available connections to the databse.

My new suggestion is to use psycopg2 and the `with` statement to ensure that connections are efficient and handled properly by garbage collection when they are no longer in use.

http://initd.org/psycopg/docs/usage.html#with-statement

See also the psycopg2 list of Best Practices:

http://initd.org/psycopg/docs/faq.html#best-practices

### OUTDATED: The ideas below are out of date...  see above

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
