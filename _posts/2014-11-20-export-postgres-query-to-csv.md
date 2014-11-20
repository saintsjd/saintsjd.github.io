Postgres makes it easy to export a query to CSV. Here is how I connect to a remote postgresql server and export a query to a CSV file on my local machine. 

First, connect to the remote database server:

~~~
psql -h REMOTE_SERVER_ADDRESS -U DB_USER -W
~~~

Then, export a query to CSV... ```\copy``` makes the file go to your local machine rather than the database server file system

~~~
\copy (select * from my_table limit 10) TO '~/Downloads/export.csv' CSV HEADER
~~~
