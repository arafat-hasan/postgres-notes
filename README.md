
## Install PostgreSQL in Ubuntu

To install PostgreSQL, first refresh your server’s local package index:
```
$ sudo apt update
```
Then, install the Postgres package along with a -contrib package that adds some additional utilities and functionality:
```
$ sudo apt install postgresql postgresql-contrib
```

See More: [How To Install PostgreSQL on Ubuntu 20.04 ](https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart)


## Install PostgreSQL in CentOS


### Method 1: PostgreSQL Yum Repository

Install the repository RPM:
```
$ sudo dnf install https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```
Disable the built-in PostgreSQL module:
```
$ sudo dnf -qy module disable postgresql
```
Install PostgreSQL:
```
$ sudo dnf install postgresql12-server
```

Now PostgreSQL is installed, we have to perform some initialization steps to prepare a new database cluster for PostgreSQL.

See More:  [Linux downloads (Red Hat family)](https://www.postgresql.org/download/linux/redhat/)

### Method 2: Using DNF

In DNF, CentOS 8’s default package manager, _modules_ are special collections of RPM packages that together make up a larger application. This is intended to make installing packages and their dependencies more intuitive for users.

List out the available streams for the `postgresql` module using the `dnf` command:

```
$ sudo dnf module list postgresql
```
_Output_
```
postgresql                           9.6                             client, server [d]                          PostgreSQL server and client module                         
postgresql                           10 [d]                          client, server [d]                          PostgreSQL server and client module                         
postgresql                           12                              client, server                              PostgreSQL server and client module
```

We can see in this output that there are three versions of PostgreSQL available from the **AppStream** repository: `9.6`, `10`, and `12`. The stream that provides Postgres version 10 is the default, as indicated by the `[d]` following it. If we want to install that version we could just run `sudo dnf install postgresql-server` and move on to the next step.

To install PostgreSQL version 12, you must enable that version’s module stream. When you enable a module stream, you override the default stream and make all of the packages related to the enabled stream available on the system.

To enable the module stream for Postgres version 12, run the following command:
```
$ sudo dnf module enable postgresql:12
```

_Output_
```
====================================================================
 Package        Architecture  Version          Repository      Size
====================================================================
Enabling module streams:
 postgresql                   12                                   

Transaction Summary
====================================================================

Is this ok [y/N]: y

```

After enabling the version 12 module stream, you can install the `postgresql-server` package to install PostgreSQL 12 and all of its dependencies:

```
$ sudo dnf install postgresql-server
```

When given the prompt, confirm the installation by pressing `y` then `ENTER`:

_Output_
```
. . .
Install  4 Packages

Total download size: 16 M
Installed size: 62 M
Is this ok [y/N]: y

```
Now PostgreSQL is installed, we have to perform some initialization steps to prepare a new database cluster for PostgreSQL.

See More:  [How To Install and Use PostgreSQL on CentOS 8](How%20To%20Install%20and%20Use%20PostgreSQL%20on%20CentOS%208)

## Creating a New PostgreSQL Database Cluster

We have to create a new PostgreSQL database cluster before we can start creating tables and loading them with data. A database cluster is a collection of databases that are managed by a single server instance. Creating a database cluster consists of creating the directories in which the database data will be placed, generating the shared catalog tables, and creating the `template1` and `postgres` databases.

The `template1` database is a template of sorts used to create new databases; everything that is stored in `template1`, even objects we add ourself, will be placed in new databases when they’re created. The `postgres` database is a default database designed for use by users, utilities, and third-party applications.

The Postgres package we installed in the previous step comes with a handy script called `postgresql-setup` which helps with low-level database cluster administration.

#### To create a database cluster, run the script using `sudo` and with the `--initdb` option.

If PostgreSQL is installed using PostgreSQL Yum repository:

```
$ /usr/pgsql-12/bin/postgresql-12-setup initdb
```
If PostgreSQL is installed using DNF:
```
$ sudo postgresql-setup --initdb
```

Output
```
 * Initializing database in '/var/lib/pgsql/data'
 * Initialized, logs are in /var/lib/pgsql/initdb_postgresql.log
```


#### Now start and enable PostgreSQL using systemctl.
If PostgreSQL is installed using PostgreSQL Yum repository:

```
$ systemctl enable postgresql-12
$ systemctl start postgresql-12
```
If PostgreSQL is installed using DNF:
```
$ sudo systemctl start postgresql
$ sudo systemctl enable postgresql
```

Output
```
Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /usr/lib/systemd/system/postgresql.service.
```


Now that PostgreSQL is up and running, we will go over using roles to learn how Postgres works and how it is different from similar database management systems.



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



# Creating a New postgres Role

To log in with ident-based authentication, we will need a Linux user with the same name as our Postgres role and database.

If we don’t have a matching Linux user available, you have to create one with the adduser command.
```
[arafat@server ~]$ sudo adduser postgresuser
```

We showed how to create a unix user named `postgresuser` here, but we will not use it. Instead , we will use the existing `arafat` user for a new postgres roll.

Now we will create a postgres role. After switching to `postgres` linux user:
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
```
[arafat@server ~]$ psql
psql (12.3)
Type &quot;help&quot; for help.

arafat=#
```



# Some Very First Commads
- `\q`: Quit/Exit
- `\l`:  List all databases in the current PostgreSQL database server
- `\h`: Show the list of SQLcommands
- `\c __database__`:  Connect to a database
- `\d`: To describe (list) what tables are in the database
- `\d __table__`: Show table definition (columns, etc.) including triggers
- `\i __filename__`: to run (include) a script file of SQL commands
- `\w __filename__`: To write the last SQL command to a file
- `\h _command_`: Show syntax on this SQL command
- `\?`: Show the list of \postgres commands

#### Example
```
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

```

#### Create Database
```
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
 test         | arafat       | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
(5 rows)

arafat=# \c test;
You are now connected to database &quot;test&quot; as user &quot;arafat&quot;.
```

#### Delete database
```
arafat_hasan=# DROP DATABASE test;
DROP DATABASE
```


### Create Table in database

We have to create a database again as we deleted the `test` database in previous step. Here `\d` a list of tables, and there is no table in our newly created database.
```
arafat=# CREATE DATABASE test;
CREATE DATABASE
test=# \d
Did not find any relations.
```

Now here we are creating a new table named `person`:

```sql
CREATE TABLE person (
 id BIGSERIAL NOT NULL PRIMARY KEY,
 first_name VARCHAR(50) NOT NULL,
 last_name VARCHAR(50) NOT NULL,
 gender VARCHAR(50) NOT NULL,
 date_of_birth DATE NOT NULL,
 email VARCHAR(150));
```
_Output_
```
CREATE TABLE
```

Now check list of tables:
```
test=# \d
                List of relations
 Schema |     Name      |   Type   |    Owner     
--------+---------------+----------+--------------
 public | person        | table    | arafat_hasan
 public | person_id_seq | sequence | arafat_hasan
(2 rows)
```

`person_id_seq` is not a table, it is a sequence to maintain and increment the `BIGSERIAL` value in the `person` table. The keyword `NOT NULL` means that this field can not be null or blank, we must have enter data for that field. Someone may not have email, so have kept the field as optional.

#### Table definition
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


#### INSERT INTO
Notice that, as email is not `NOT NULL` so it is optional to insert in to table.
```sql
INSERT INTO person (first_name, last_name, gender, date_of_birth)
 VALUES('Anne', 'Smith', 'female', DATE '1988-01-09');
```
_Output_
```
INSERT 0 1
```
```sql
INSERT INTO person (first_name, last_name, gender, date_of_birth, email)
 VALUES('Jack', 'Doe', 'male', DATE '1985-11-03', 'jack@example.com');
```
_Output_
```
INSERT 0 1
```
#### SELECT
Fetch all data from table:
```sql
SELECT * FROM person;
```
_Output_
```
 id | first_name | last_name | gender | date_of_birth |      email       
----+------------+-----------+--------+---------------+------------------
  1 | Anne       | Smith     | female | 1988-01-09    | 
  2 | Jack       | Doe       | male   | 1985-11-03    | jack@example.com
(2 rows)
```



### Mockaroo
We will use a site  named [Mockaroo](https://mockaroo.com/) to insert a lot of data into our table for our learning convenience. Mockaroo is a online realist test data generator. We will download a bunch of dummy but realistic data in sql format and execute the sql file in terminal.

![Generate data using Mockaroo](https://imgur.com/rLGnx8z.jpg, "Generate data using Mockaroo")

Notes:
- Field Names and types in Mockaroo according to the image above.
-  For our learning convenience, make sure 30% blank in `email` field.
- Set a  acceptable nice range for data of birth.
- To find type of each Field, you have to search with appropriate keyword.
- Tick the _include create table_ option.

Now Download data as file named `person.sql`.



#### Drop table
As we included _include create table_ option in mockaroo website, the sql file we have downloaded includes CREATE TABLE command. So we need to DROP our previous table.
```
test=# DROP TABLE person;
DROP TABLE
```
Now we will do some tweaking in `person.sql`, according to our needs. Open this file in your preferred editor, I'm using vs code. Then make the following changes to the CREATE TABLE command at the top of the file.  Notice that we have added `id BIGSERIAL NOT NULL PRIMARY KEY`, changed `VARCHAR` sizes and specified the `NOT NULL` fields.

```sql
create table person (
	id BIGSERIAL NOT NULL PRIMARY KEY,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(50) NOT NULL,
	email VARCHAR(150),
	gender VARCHAR(7) NOT NULL,
	date_of_birth DATE NOT NULL,
	country_of_birth VARCHAR(50) NOT NULL
);
-- More lines containing INSERT command, We are not showing them here.
```

After saving our changed `person.sql` file, now we will execute it. We can copy the whole file and paste it into posges terminal, that will work too, but we are going to do it in a better way. `\i` excecutes a script file of SQL commands.
```
test=# \i /path/to/person.sql 
```
Now the script `person.sql` is executed, and there are 1000 rows in the person table.

Now out table description look like this:
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

#### SELECT
Here we have made a query to fetch all the data from the `person` table.
```sql
SELECT * FROM person;
```
_Output_
```
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

Alternatively, we can specify our required field names:
```sql
SELECT id, first_name, last_name FROM person;
```
_Output_
```
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


## ORDER BY

The ORDER BY keyword is used to sort the result-set in ascending (`ASC`) or descending (`DESC`) order. The ORDER BY keyword sorts the records in ascending order by default. To sort the records in ascending order, use the DESC keyword.


Ascending order:
```sql
SELECT * FROM person ORDER BY country_of_birth;
```

_Output_
```
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
--More--
```

Dscending order:
```sql
SELECT * FROM person ORDER BY country_of_birth DESC;
```

_Output_
```
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

Date of birth in dscending order:
```sql
SELECT * FROM person ORDER BY date_of_birth DESC;
```
_Output_

```
  id  |   first_name   |      last_name      |                  email                  | gender | date_of_birth |         country_of_birth         
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
  813 | Terri          | Blockey             | tblockeymk@gnu.org                      | Female | 2010-04-08    | China
--More--
```



`ORDER BY` with two-parameter. This means that if `country_of_birth` is the same, then the rows will be sorted according to the `id` column. Check the difference with pervious one and this.
```sql
SELECT * FROM person ORDER BY country_of_birth, id;
```

_Output_
```
  id  |   first_name   |      last_name      |                  email                  | gender | date_of_birth |         country_of_birth         
------+----------------+---------------------+-----------------------------------------+--------+---------------+----------------------------------
  223 | Collen         | Raubheim            | craubheim66@gravatar.com                | Female | 1968-01-31    | Afghanistan
  475 | Koren          | Burgen              |                                         | Female | 1985-09-16    | Afghanistan
  331 | Vaughan        | Borles              | vborles96@behance.net                   | Male   | 1987-09-08    | Albania
  583 | Nikolia        | Whodcoat            | nwhodcoatg6@army.mil                    | Female | 1993-01-01    | Albania
  662 | Una            | Chevis              | uchevisid@whitehouse.gov                | Female | 2001-10-03    | Albania
  831 | Cordy          | Aries               |                                         | Male   | 2007-07-06    | Albania
--More--
```

#### DISTINCT
The `SELECT DISTINCT` statement is used to return only distinct (different) values.
```sql
SELECT DISTINCT country_of_birth FROM person ORDER BY country_of_birth;
```

_Output_
```
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
--More--
```
#### WHERE
The `WHERE` clause is used to extract only those records that fulfill a specified condition.
```sql
SELECT * FROM person WHERE gender='Female';
```

_Output_
```
 id  |   first_name   |      last_name      |                 email                 | gender | date_of_birth |     country_of_birth     
-----+----------------+---------------------+---------------------------------------+--------+---------------+--------------------------
   1 | Ronda          | Skermer             | rskermer0@arstechnica.com             | Female | 1993-06-30    | Argentina
   4 | Erminie        | M'Quharg            | emquharg3@e-recht24.de                | Female | 1999-03-13    | Mozambique
   8 | Letta          | Caurah              | lcaurah7@yale.edu                     | Female | 1994-09-09    | Indonesia
   9 | Elset          | Agass               | eagass8@rambler.ru                    | Female | 2004-06-26    | China
  10 | Aurore         | Drillingcourt       | adrillingcourt9@cnet.com              | Female | 1977-10-19    | China
  11 | Ilse           | Goldman             | igoldmana@ihg.com                     | Female | 2001-07-31    | Mongolia
  12 | Penny          | Nayer               | pnayerb@harvard.edu                   | Female | 1969-02-05    | Colombia
--More--
```



#### BETWEEN
The `BETWEEN` operator selects values within a given range. The values can be numbers, text, or dates.

The `BETWEEN` operator is inclusive: begin and end values are included.

```sql
SELECT * FROM person WHERE date_of_birth BETWEEN '1985-02-02' AND '1986-06-04';
```

_Output_
```
 id  | first_name |  last_name   |            email             | gender | date_of_birth |   country_of_birth    
-----+------------+--------------+------------------------------+--------+---------------+-----------------------
  25 | Billi      | Dybbe        | bdybbeo@samsung.com          | Female | 1986-02-22    | Brazil
  37 | Sorcha     | Tunesi       | stunesi10@adobe.com          | Female | 1986-04-12    | Philippines
  45 | Carleen    | Dzeniskevich | cdzeniskevich18@disqus.com   | Female | 1985-06-18    | China
 103 | Oberon     | Sparry       | osparry2u@yellowbook.com     | Male   | 1985-09-22    | China
 125 | Cal        | Shurville    | cshurville3g@1und1.de        | Male   | 1986-01-29    | Qatar
 157 | Juline     | Wanek        |                              | Female | 1985-11-30    | Sweden
 162 | Amelia     | Braferton    |                              | Female | 1986-05-03    | New Zealand
 168 | West       | Glowacz      | wglowacz4n@yolasite.com      | Male   | 1985-12-02    | Canada
--More--
```

#### LIKE
The `LIKE` operator is used in a `WHERE` clause to search for a specified pattern in a column.

There are two wildcards often used in conjunction with the LIKE operator:

-   %: The percent sign represents zero, one, or multiple characters
-   _ : The underscore represents a single character

Find all emails ending with `disqus.com`:
```sql
SELECT * FROM person WHERE email LIKE '%disqus.com';
```
_Output_
```
 id  | first_name |  last_name   |           email            | gender | date_of_birth | country_of_birth 
-----+------------+--------------+----------------------------+--------+---------------+------------------
  45 | Carleen    | Dzeniskevich | cdzeniskevich18@disqus.com | Female | 1985-06-18    | China
 852 | Alex       | Garmans      | agarmansnn@disqus.com      | Male   | 1990-11-08    | China
(2 rows)
```


#### GROUP BY
The `GROUP BY` statement groups rows that have the same values into summary rows, like "find the number of person in each country".

The `GROUP BY` statement is often used with aggregate functions (`COUNT`, `MAX`, `MIN`, `SUM`, `AVG`) to group the result-set by one or more columns.

```sql
SELECT country_of_birth, COUNT(*) FROM person GROUP BY country_of_birth;
```

_Output_

```
         country_of_birth         | count 
----------------------------------+-------
 Bangladesh                       |     1
 Indonesia                        |   109
 Venezuela                        |     5
 Cameroon                         |     3
 Czech Republic                   |    18
 Sweden                           |    31
 Dominican Republic               |     7
 Ireland                          |     3
 Macedonia                        |     4
 Papua New Guinea                 |     2
 Sri Lanka                        |     1
--More--
```

```sql
SELECT country_of_birth, COUNT(*) FROM person GROUP BY country_of_birth ORDER BY country_of_birth;
```

_Output_
```
         country_of_birth         | count 
----------------------------------+-------
 Afghanistan                      |     2
 Albania                          |     5
 Algeria                          |     1
 Angola                           |     2
 Argentina                        |    20
 Armenia                          |     5
 Australia                        |     1
 Azerbaijan                       |     3
 Bangladesh                       |     1
--More--
```

#### GROUP BY HAVING
The HAVING clause was added to SQL because the WHERE keyword could not be used with aggregate functions.

```sql
SELECT country_of_birth, COUNT(*) FROM person GROUP BY country_of_birth HAVING COUNT(*) > 50 ORDER BY country_of_birth;
```
_Output_
```
 country_of_birth | count 
------------------+-------
 China            |   180
 Indonesia        |   109
 Russia           |    56
(3 rows)
```

COALESCE
The `COALESCE()` function returns the first non-null value in a list.
```sql
SELECT COALESCE(email, 'Email not provided') FROM person;
```

_Output_
```
                coalesce                 
-----------------------------------------
 rskermer0@arstechnica.com
 habbett1@cbc.ca
 fnickerson2@mac.com
 emquharg3@e-recht24.de
 Email not provided
 ramesbury5@businessinsider.com
 Email not provided
 lcaurah7@yale.edu
 eagass8@rambler.ru
 adrillingcourt9@cnet.com
 igoldmana@ihg.com
 pnayerb@harvard.edu
--More--
```


Now we will download a new bunch of data to ceate another  table called `car`. This table have this columns:
- `id`: Primary key
- `make`: Company name of the car
- `model`: Model of the car
- `price`: Price of the car, price between in a nice range

![Generate data using Mockaroo](https://imgur.com/z93rIG7.jpg ":Generate data using Mockaroo")

Now edit the downloded file `car.sql` a bit—
```sql
create table car (
	id BIGSERIAL NOT NULL PRIMARY KEY,
	make VARCHAR(100) NOT NULL,
	model VARCHAR(100) NOT NULL,
	price NUMERIC(19, 2) NOT NULL
);

-- More lines containing INSERT command, We are not showing them here.
```
After saving our changed `car.sql` file, now we will execute it.
```
test=# \i /path/to/car.sql 
```

Here is first 10 rows from `car` table. `LIMIT` is used to get only first 10 rows.
```sql
SELECT * FROM car LIMIT 10;
```
_Ouptut_
```
 id |    make    |      model       |   price   
----+------------+------------------+-----------
  1 | Daewoo     | Leganza          | 241058.40
  2 | Mitsubishi | Montero          | 269595.21
  3 | Kia        | Rio              | 245275.16
  4 | GMC        | Savana 1500      | 217435.26
  5 | Jaguar     | X-Type           |  41665.96
  6 | Lincoln    | Mark VIII        | 163843.38
  7 | GMC        | Rally Wagon 3500 | 231169.05
  8 | Cadillac   | Escalade ESV     | 279951.34
  9 | Volvo      | XC70             | 269436.96
 10 | Isuzu      | Rodeo            |  65421.58
(10 rows)
```

#### MAX

The `MAX()` function returns the largest value of the selected column.
```sql
SELECT MAX(price) FROM car;
```
_Ouptut_
```
    max    
-----------
 299959.83
(1 row)

```

```sql
SELECT make, MAX(price) FROM car GROUP BY make LIMIT 5;
```
_Ouptut_
```
   make   |    max    
----------+-----------
 Ford     | 290993.39
 Smart    | 159887.95
 Maserati | 221349.10
 Dodge    | 299766.43
 Infiniti | 298245.19
(5 rows)
```

#### MIN
The `MIN()` function returns the smallest value of the selected column.
```sql
SELECT MIN(price) FROM car;
```
_Ouptut_
```
   min    
----------
 30348.16
(1 row)

```

```sql
SELECT make, MIN(price) FROM car GROUP BY make LIMIT 5;
```
_Ouptut_
```
   make   |    min    
----------+-----------
 Ford     |  31021.48
 Smart    | 159887.95
 Maserati |  38668.83
 Dodge    |  33495.17
 Infiniti |  47912.88
(5 rows)
```

#### AVG
The `AVG()` function returns the average value of a numeric column.
```sql
SELECT AVG(price) FROM car;
```
_Ouptut_
```
         avg         
---------------------
 164735.601300000000
(1 row)
```

```sql
SELECT make, AVG(price) FROM car GROUP BY make LIMIT 5;
```
_Ouptut_
```
   make   |         avg         
----------+---------------------
 Ford     | 171967.729473684211
 Smart    | 159887.950000000000
 Maserati | 122897.857500000000
 Dodge    | 166337.502307692308
 Infiniti | 179690.643846153846
(5 rows)
```


#### ROUND
The PostgreSQL `ROUND()` function rounds a numeric value to its nearest integer or a number with the number of decimal places.
```sql
SELECT ROUND(AVG(price)) FROM car;
```
_Ouptut_
```
 round  
--------
 164736
(1 row)
```

```sql
SELECT make, ROUND(AVG(price)) FROM car GROUP BY make LIMIT 5;
```
_Ouptut_
```
   make   | round  
----------+--------
 Ford     | 171968
 Smart    | 159888
 Maserati | 122898
 Dodge    | 166338
 Infiniti | 179691
(5 rows)


```

#### COUNT
The `COUNT()` function returns the number of rows that matches a specified criterion.
```sql
SELECT COUNT(make) FROM car;
```
_Ouptut_
```
 count 
-------
  1000
(1 row)
```

#### SUM
The `SUM()` function returns the total sum of a numeric column.
```sql
SELECT SUM(price) FROM car;
```
_Ouptut_
```
     sum      
--------------
 164735601.30
(1 row)
```

```sql
SELECT make, SUM(price) FROM car GROUP BY make LIMIT 5;
```
_Ouptut_
```
   make   |     sum     
----------+-------------
 Ford     | 16336934.30
 Smart    |   159887.95
 Maserati |   491591.43
 Dodge    |  8649550.12
 Infiniti |  2335978.37
(5 rows)
```

### Basic arithmatic operation

```sql
SELECT 10 + 2;
```
_Ouptut_
```
 ?column? 
----------
       12
(1 row)
```

```sql
SELECT 10 / 2;
```

_Ouptut_
```
 ?column? 
----------
        5
(1 row)
```

```sql
SELECT 10^2;
```

_Ouptut_
```
 ?column? 
----------
      100
(1 row)
```



Now suppose the company offers a 10% discount on all cars. We will now calculate the amount of this 10%, and calculate the price of the new discount.

```sql
SELECT id, make, model, price, ROUND(price * 0.10, 2), ROUND(price - (price * 0.10), 2) FROM car;
```

_Ouptut_
```
  id  |     make      |        model         |   price   |  round   |   round   
------+---------------+----------------------+-----------+----------+-----------
    1 | Daewoo        | Leganza              | 241058.40 | 24105.84 | 216952.56
    2 | Mitsubishi    | Montero              | 269595.21 | 26959.52 | 242635.69
    3 | Kia           | Rio                  | 245275.16 | 24527.52 | 220747.64
    4 | GMC           | Savana 1500          | 217435.26 | 21743.53 | 195691.73
    5 | Jaguar        | X-Type               |  41665.96 |  4166.60 |  37499.36
    6 | Lincoln       | Mark VIII            | 163843.38 | 16384.34 | 147459.04
    7 | GMC           | Rally Wagon 3500     | 231169.05 | 23116.91 | 208052.15
    8 | Cadillac      | Escalade ESV         | 279951.34 | 27995.13 | 251956.21
    9 | Volvo         | XC70                 | 269436.96 | 26943.70 | 242493.26
   10 | Isuzu         | Rodeo                |  65421.58 |  6542.16 |  58879.42
--More--
```

`ROUND (source [ , n ] )` function rounds a numeric value to its nearest integer or a number with the number of decimal places. Where The source argument is a number or a numeric expression that is to be rounded and the n argument is an integer that determines the number of decimal places after rounding.



ALIAS
SQL aliases are used to give a table, or a column in a table, a temporary name. Aliases are often used to make column names more readable. An alias only exists for the duration of the query.
```sql
SELECT id, make, model, price AS original_price,
 ROUND(price * 0.10, 2) AS ten_percent_discount,
 ROUND(price - (price * 0.10), 2) AS discounted_price
 FROM car;
```

_Ouptut_
```
  id  |     make      |        model         | original_price | ten_percent_discount | discounted_price 
------+---------------+----------------------+----------------+----------------------+------------------
    1 | Daewoo        | Leganza              |      241058.40 |             24105.84 |        216952.56
    2 | Mitsubishi    | Montero              |      269595.21 |             26959.52 |        242635.69
    3 | Kia           | Rio                  |      245275.16 |             24527.52 |        220747.64
    4 | GMC           | Savana 1500          |      217435.26 |             21743.53 |        195691.73
    5 | Jaguar        | X-Type               |       41665.96 |              4166.60 |         37499.36
    6 | Lincoln       | Mark VIII            |      163843.38 |             16384.34 |        147459.04
    7 | GMC           | Rally Wagon 3500     |      231169.05 |             23116.91 |        208052.15
    8 | Cadillac      | Escalade ESV         |      279951.34 |             27995.13 |        251956.21
    9 | Volvo         | XC70                 |      269436.96 |             26943.70 |        242493.26
   10 | Isuzu         | Rodeo                |       65421.58 |              6542.16 |         58879.42
--More--
```



