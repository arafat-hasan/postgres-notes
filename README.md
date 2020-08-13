





```
[arafat@server ~]$ dnf install https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
[arafat@server ~]$ dnf -qy module disable postgresql
[arafat@server ~]$ dnf install postgresql12-server
```
We have to create a new PostgreSQL database cluster before we can start creating tables and loading them with data. A database cluster is a collection of databases that are managed by a single server instance. Creating a database cluster consists of creating the directories in which the database data will be placed, generating the shared catalog tables, and creating the `template1` and `postgres` databases.

The `template1` database is a template of sorts used to create new databases; everything that is stored in `template1`, even objects we add ourself, will be placed in new databases when they’re created. The `postgres` database is a default database designed for use by users, utilities, and third-party applications.

The Postgres package we installed in the previous step comes with a handy script called `postgresql-setup` which helps with low-level database cluster administration. To create a database cluster, run the script using `sudo` and with the `--initdb` option:

```
[arafat@server ~]$ /usr/pgsql-12/bin/postgresql-12-setup initdb

```

Output
```
 * Initializing database in '/var/lib/pgsql/data'
 * Initialized, logs are in /var/lib/pgsql/initdb_postgresql.log
```


Now start and enable PostgreSQL using systemctl:
```
[arafat@server ~]$ systemctl enable postgresql-12
[arafat@server ~]$ systemctl start postgresql-12
```

Output
```
Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /usr/lib/systemd/system/postgresql.service.
```


Now that PostgreSQL is up and running, we will go over using roles to learn how Postgres works and how it is different from similar database management systems you may have used in the past.

# Understanding PostgreSQL roles and databases

By default, Postgres uses a concept called roles to handle in authentication and authorization. These are, in some ways, similar to regular Unix-style accounts, but Postgres does not distinguish between users and groups and instead prefers the more flexible term role.

Upon installation, Postgres is set up to use `ident` authentication, meaning that it associates Postgres roles with a matching Unix/Linux system account. If a role exists within Postgres, a Unix/Linux username with the same name is able to sign in as that role.


The installation procedure created a user account called `postgres` that is associated with the default Postgres role. In order to use Postgres, at first we have to log in using that role.

So we have to switch over to the `postgres` unix user, which is created upon installation of Postgres and then from the `postgres` unix user  we will able to log on postgres server.

```
[arafat@server ~]$ sudo -i -u postgres
[postgres@server ~]$ psql
```

Alternatively, to access a Postgres prompt without switching users

```
[arafat@server ~]$ sudo -u postgres psql
```



# Creating a New Postgres Role

To log in with ident-based authentication, we will need a Linux user with the same name as our Postgres role and database.

If we don’t have a matching Linux user available, you have to create one with the adduser command.
```
[arafat@server ~]$ sudo adduser arafat
```


We have to create a postgres role. After switching to `postgres` linux user:
```
postgres@server:~$ createuser --interactive
Enter name of role to add: arafat
Shall the new role be a superuser? (y/n) y
```


Another assumption that the Postgres authentication system makes by default is that for any role used to log in, that role will have a database with the same name which it can access.

This means that if the user we created in the last section is called `arafat`, that role will attempt to connect to a database which is also called `arafat` by default. We can create such a database with the createdb command.

If we are logged in as the postgres account, we would type something like:

```
postgres@server:~$ createdb arafat
```


Now we will able to connect to `psql` from unix user `arafat` to Postgres role `arafat`.




# Some Work



<pre>
[arafat@server ~]$ 
[arafat@server ~]$ psql
psql (12.3)
Type &quot;help&quot; for help.

arafat=# \l
                                   List of databases
     Name     |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
--------------+----------+----------+-------------+-------------+-----------------------
 arafat       | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 postgres     | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0    | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
              |          |          |             |             | postgres=CTc/postgres
 template1    | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
              |          |          |             |             | postgres=CTc/postgres
(4 rows)

arafat=# \h CREATE DATABASE;
Command:     CREATE DATABASE
Description: create a new database
Syntax:
CREATE DATABASE name
    [ [ WITH ] [ OWNER [=] user_name ]
           [ TEMPLATE [=] template ]
           [ ENCODING [=] encoding ]
           [ LC_COLLATE [=] lc_collate ]
           [ LC_CTYPE [=] lc_ctype ]
           [ TABLESPACE [=] tablespace_name ]
           [ ALLOW_CONNECTIONS [=] allowconn ]
           [ CONNECTION LIMIT [=] connlimit ]
           [ IS_TEMPLATE [=] istemplate ] ]

URL: https://www.postgresql.org/docs/12/sql-createdatabase.html

arafat=# CREATE DATABASE test;
CREATE DATABASE
arafat=# \l
                                     List of databases
     Name     |    Owner     | Encoding |   Collate   |    Ctype    |   Access privileges   
--------------+--------------+----------+-------------+-------------+-----------------------
 arafat       | postgres     | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 postgres     | postgres     | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0    | postgres     | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
              |              |          |             |             | postgres=CTc/postgres
 template1    | postgres     | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
              |              |          |             |             | postgres=CTc/postgres
 test         | arafat | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
(5 rows)

arafat=# \c test;
You are now connected to database &quot;test&quot; as user &quot;arafat&quot;.
</pre>



