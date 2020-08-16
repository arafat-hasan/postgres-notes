





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




Delete database
arafat_hasan=# DROP DATABASE test;
DROP DATABASE



Create Table in database

```
test=# \d
Did not find any relations.


```


````



test=# CREATE TABLE person (
test(# id INT,
test(# first_name VARCHAR(50),
test(# last_name VARCHAR(50),
test(# gender VARCHAR(7),
test(# date_of_birth DATE );
CREATE TABLE
test=# 

```

```
test=# CREATE TABLE person (
test(#  id BIGSERIAL NOT NULL PRIMARY KEY,
test(#  first_name VARCHAR(50) NOT NULL,
test(#  last_name VARCHAR(50) NOT NULL,
test(#  gender VARCHAR(50) NOT NULL,
test(#  date_of_birth DATE NOT NULL,
test(#  email VARCHAR(150));
CREATE TABLE
```

```
test=# \d
                List of relations
 Schema |     Name      |   Type   |    Owner     
--------+---------------+----------+--------------
 public | person        | table    | arafat_hasan
 public | person_id_seq | sequence | arafat_hasan
(2 rows)

```
`person_id_seq` is not a table, it is a sequence to maintain and increment the `BIGSERIAL` value in the `person` table.

```
test=#  \d person;
                                       Table "public.person"
    Column     |          Type          | Collation | Nullable |              Default               
---------------+------------------------+-----------+----------+------------------------------------
 id            | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name    | character varying(50)  |           | not null | 
 last_name     | character varying(50)  |           | not null | 
 gender        | character varying(50)  |           | not null | 
 date_of_birth | date                   |           | not null | 
 email         | character varying(150) |           |          | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id)

```




```
test=# INSERT INTO person (first_name, last_name, gender, date_of_birth)
test-# VALUES('Anne', 'Smith', 'female', DATE '1988-01-09');
INSERT 0 1

```
```
test=# INSERT INTO person (first_name, last_name, gender, date_of_birth, email)
VALUES('Jack', 'Doe', 'male', DATE '1985-11-03', 'jack@example.com');
INSERT 0 1
```

```
test=# SELECT * FROM person;
 id | first_name | last_name | gender | date_of_birth |      email       
----+------------+-----------+--------+---------------+------------------
  1 | Anne       | Smith     | female | 1988-01-09    | 
  2 | Jack       | Doe       | male   | 1985-11-03    | jack@example.com
(2 rows)


```

Generate 1000 Rows with Mockaroo. Mockaroo is a online realist test data generator. By using this site, we will download a bunch of dummy but realistic data in sql format and execute the sql file in termianl.

```
test=# DROP TABLE person;
DROP TABLE
test=# \i /home/arafat_hasan/Downloads/person.sql 
```

Here we have droped our previous table named person. From the Mockaroo site, we have downloaded sql command with create table included.


Now out table is:

```
test=# \d person;
                           Table "public.person"
      Column      |          Type          | Collation | Nullable | Default 
------------------+------------------------+-----------+----------+---------
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 

```


```
test=# SELECT * FROM person;
  id  |   first_name   |      last_name      |                  email                  | gender | date_of_birth |         country_of_birth         
------+----------------+---------------------+-----------------------------------------+--------+---------------+----------------------------------
    1 | Ronda          | Skermer             | rskermer0@arstechnica.com               | Female | 1993-06-30    | Argentina
    2 | Hamid          | Abbett              | habbett1@cbc.ca                         | Male   | 1995-08-31    | Ethiopia
    3 | Francis        | Nickerson           | fnickerson2@mac.com                     | Male   | 1998-03-16    | Portugal
    4 | Erminie        | M'Quharg            | emquharg3@e-recht24.de                  | Female | 1999-03-13    | Mozambique
    5 | Teodoro        | Trimmill            |                                         | Male   | 1982-04-30    | China
    6 | Reilly         | Amesbury            | ramesbury5@businessinsider.com          | Male   | 1990-12-31    | China
    7 | West           | Elphey              |                                         | Male   | 2004-03-29    | Indonesia
    8 | Letta          | Caurah              | lcaurah7@yale.edu                       | Female | 1994-09-09    | Indonesia
    9 | Elset          | Agass               | eagass8@rambler.ru                      | Female | 2004-06-26    | China
   10 | Aurore         | Drillingcourt       | adrillingcourt9@cnet.com                | Female | 1977-10-19    | China
   11 | Ilse           | Goldman             | igoldmana@ihg.com                       | Female | 2001-07-31    | Mongolia
   12 | Penny          | Nayer               | pnayerb@harvard.edu                     | Female | 1969-02-05    | Colombia
   13 | Neale          | Dubery              | nduberyc@soundcloud.com                 | Male   | 1975-12-22    | Portugal
   14 | Gnni           | Dickman             | gdickmand@people.com.cn                 | Female | 1977-10-12    | Guatemala
   15 | Flori          | Giroldi             | fgiroldie@ameblo.jp                     | Female | 1975-11-14    | China
--More--
```


```
test=# SELECT id, first_name, last_name FROM person;
  id  |   first_name   |      last_name      
------+----------------+---------------------
    1 | Ronda          | Skermer
    2 | Hamid          | Abbett
    3 | Francis        | Nickerson
    4 | Erminie        | M'Quharg
    5 | Teodoro        | Trimmill
    6 | Reilly         | Amesbury
    7 | West           | Elphey
    8 | Letta          | Caurah
    9 | Elset          | Agass
   10 | Aurore         | Drillingcourt
--More--
```


# Order BY

Order by ASC DESC

```
test=# SELECT * FROM person ORDER BY country_of_birth;
  id  |   first_name   |      last_name      |                  email                  | gender | date_of_birth |         country_of_birth         
------+----------------+---------------------+-----------------------------------------+--------+---------------+----------------------------------
  475 | Koren          | Burgen              |                                         | Female | 1985-09-16    | Afghanistan
  223 | Collen         | Raubheim            | craubheim66@gravatar.com                | Female | 1968-01-31    | Afghanistan
  331 | Vaughan        | Borles              | vborles96@behance.net                   | Male   | 1987-09-08    | Albania
  831 | Cordy          | Aries               |                                         | Male   | 2007-07-06    | Albania
  662 | Una            | Chevis              | uchevisid@whitehouse.gov                | Female | 2001-10-03    | Albania
  993 | Delmar         | Sparham             |                                         | Male   | 2000-01-24    | Albania
  583 | Nikolia        | Whodcoat            | nwhodcoatg6@army.mil                    | Female | 1993-01-01    | Albania
  751 | Kyrstin        | Wimpenny            | kwimpennyku@slideshare.net              | Female | 1986-07-12    | Algeria
  837 | Dalis          | McLinden            |                                         | Male   | 1989-09-24    | Angola
  965 | Morlee         | Flintoffe           | mflintoffeqs@bing.com                   | Male   | 1999-12-23    | Angola
  636 | Steffen        | Bohman              | sbohmanhn@cmu.edu                       | Male   | 2001-05-07    | Argentina
  384 | Putnam         | Cowin               | pcowinan@freewebs.com                   | Male   | 2007-05-10    | Argentina
  305 | Godiva         | Scotchbrook         | gscotchbrook8g@cbslocal.com             | Female | 1977-05-26    | Argentina
  800 | Letizia        | Dobell              |                                         | Female | 1971-07-15    | Argentina
  597 | Willard        | Brydon              | wbrydongk@miitbeian.gov.cn              | Male   | 1997-01-07    | Argentina
  357 | Adelle         | Beden               | abeden9w@eepurl.com                     | Female | 1992-03-13    | Argentina
  882 | Myrlene        | Toffano             | mtoffanooh@biblegateway.com             | Female | 1988-08-18    | Argentina
  167 | Tanney         | Nerney              | tnerney4m@slideshare.net                | Male   | 1987-08-15    | Argentina
  176 | Stevy          | Hawley              |                                         | Male   | 1968-11-11    | Argentina
  178 | Crysta         | Lant                |                                         | Female | 2003-07-03    | Argentina
  289 | Fremont        | Beeble              | fbeeble80@storify.com                   | Male   | 1993-09-15    | Argentina
  786 | Delbert        | Gramer              | dgramerlt@nymag.com                     | Male   | 2009-05-15    | Argentina
  664 | Marsh          | MacGillicuddy       |                                         | Male   | 1968-05-30    | Argentina
    1 | Ronda          | Skermer             | rskermer0@arstechnica.com               | Female | 1993-06-30    | Argentina
  938 | Dukie          | Cant                | dcantq1@independent.co.uk               | Male   | 1991-06-08    | Argentina
  472 | Vick           | Baggett             |                                         | Male   | 1985-10-15    | Argentina
  796 | Maximilianus   | Newlyn              | mnewlynm3@google.com.br                 | Male   | 1969-08-14    | Argentina
  474 | Pete           | Coffin              | pcoffind5@networksolutions.com          | Male   | 1977-04-15    | Argentina
  110 | Jackie         | Connochie           | jconnochie31@plala.or.jp                | Male   | 1975-11-25    | Argentina
  247 | Maren          | Mitham              | mmitham6u@lycos.com                     | Female | 2008-04-04    | Argentina

```


```
test=# SELECT * FROM person ORDER BY country_of_birth DESC;
  id  |   first_name   |      last_name      |                  email                  | gender | date_of_birth |         country_of_birth         
------+----------------+---------------------+-----------------------------------------+--------+---------------+----------------------------------
  563 | Meredeth       | Pantin              |                                         | Male   | 1971-02-22    | Zambia
  173 | Pennie         | Christauffour       | pchristauffour4s@scientificamerican.com | Male   | 2004-04-16    | Zambia
  947 | Saidee         | Daffern             | sdaffernqa@barnesandnoble.com           | Female | 1973-03-11    | Yemen
  742 | Lacee          | Sumner              | lsumnerkl@icio.us                       | Female | 2007-03-31    | Yemen
  520 | Clerissa       | Mockett             |                                         | Female | 1980-12-08    | Yemen
   89 | Robinson       | Tichner             |                                         | Male   | 2005-12-09    | Yemen
  754 | Oren           | Eidler              | oeidlerkx@typepad.com                   | Male   | 1969-02-23    | Yemen
  725 | Sadye          | Garman              |                                         | Female | 1985-11-05    | Yemen
  537 | Isadore        | Tasker              | itaskerew@example.com                   | Male   | 1977-03-05    | Vietnam
  602 | Nevins         | Blenkinship         | nblenkinshipgp@psu.edu                  | Male   | 2010-02-04    | Vietnam

```


```
test=# SELECT * FROM person ORDER BY date_of_birth DESC;
------+----------------+---------------------+-----------------------------------------+--------+---------------+----------------------------------
  307 | Penni          | Privost             |                                         | Female | 2010-08-07    | Indonesia
   43 | Kathye         | Bottleson           | kbottleson16@google.pl                  | Female | 2010-06-27    | China
  616 | Darryl         | Craw                | dcrawh3@nba.com                         | Male   | 2010-05-30    | Guatemala
  549 | Paulie         | Durante             | pdurantef8@go.com                       | Female | 2010-05-09    | Russia
  983 | Elka           | Chyuerton           |                                         | Female | 2010-04-28    | China
  533 | Leslie         | Lusgdin             | llusgdines@creativecommons.org          | Female | 2010-04-20    | Bosnia and Herzegovina
  248 | Shurwood       | Vezey               | svezey6v@amazon.com                     | Male   | 2010-04-15    | Indonesia
  974 | Noll           | Pidgin              | npidginr1@wiley.com                     | Male   | 2010-04-13    | Indonesia
  676 | Edwina         | Presdee             | epresdeeir@icio.us                      | Female | 2010-04-10    | China
```



ORDER BY with two parameter. This means that if country_of_birth is same, then the rows will be sorted according to id column. Check the difference between this.
```
test=# SELECT * FROM person ORDER BY country_of_birth, id;
  id  |   first_name   |      last_name      |                  email                  | gender | date_of_birth |         country_of_birth         
------+----------------+---------------------+-----------------------------------------+--------+---------------+----------------------------------
  223 | Collen         | Raubheim            | craubheim66@gravatar.com                | Female | 1968-01-31    | Afghanistan
  475 | Koren          | Burgen              |                                         | Female | 1985-09-16    | Afghanistan
  331 | Vaughan        | Borles              | vborles96@behance.net                   | Male   | 1987-09-08    | Albania
  583 | Nikolia        | Whodcoat            | nwhodcoatg6@army.mil                    | Female | 1993-01-01    | Albania
  662 | Una            | Chevis              | uchevisid@whitehouse.gov                | Female | 2001-10-03    | Albania
  831 | Cordy          | Aries               |                                         | Male   | 2007-07-06    | Albania
  993 | Delmar         | Sparham             |                                         | Male   | 2000-01-24    | Albania
  751 | Kyrstin        | Wimpenny            | kwimpennyku@slideshare.net              | Female | 1986-07-12    | Algeria
  837 | Dalis          | McLinden            |                                         | Male   | 1989-09-24    | Angola
  965 | Morlee         | Flintoffe           | mflintoffeqs@bing.com                   | Male   | 1999-12-23    | Angola
    1 | Ronda          | Skermer             | rskermer0@arstechnica.com               | Female | 1993-06-30    | Argentina
  110 | Jackie         | Connochie           | jconnochie31@plala.or.jp                | Male   | 1975-11-25    | Argentina
  167 | Tanney         | Nerney              | tnerney4m@slideshare.net                | Male   | 1987-08-15    | Argentina

```


```
test=# SELECT DISTINCT country_of_birth FROM person ORDER BY country_of_birth;
         country_of_birth         
----------------------------------
 Afghanistan
 Albania
 Algeria
 Angola
 Argentina
 Armenia
 Australia
 Azerbaijan
 Bangladesh
 Belarus
 Benin
 Bolivia
 Bosnia and Herzegovina
 Brazil
 Bulgaria
 Burkina Faso
 Cameroon
 Canada
 Central African Republic

```

```
test=# SELECT * FROM person where gender='Female';
 id  |   first_name   |      last_name      |                 email                 | gender | date_of_birth |     country_of_birth     
-----+----------------+---------------------+---------------------------------------+--------+---------------+--------------------------
   1 | Ronda          | Skermer             | rskermer0@arstechnica.com             | Female | 1993-06-30    | Argentina
   4 | Erminie        | M'Quharg            | emquharg3@e-recht24.de                | Female | 1999-03-13    | Mozambique
   8 | Letta          | Caurah              | lcaurah7@yale.edu                     | Female | 1994-09-09    | Indonesia
   9 | Elset          | Agass               | eagass8@rambler.ru                    | Female | 2004-06-26    | China
  10 | Aurore         | Drillingcourt       | adrillingcourt9@cnet.com              | Female | 1977-10-19    | China
  11 | Ilse           | Goldman             | igoldmana@ihg.com                     | Female | 2001-07-31    | Mongolia
  12 | Penny          | Nayer               | pnayerb@harvard.edu                   | Female | 1969-02-05    | Colombia

```
