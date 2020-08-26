

<p align="center">
<img align="center" width="250" height="250" alt="PostgreSQL Logo" src="https://imgur.com/JL46J3e.jpg">
<p>



<h1 align="center"> PostgreSQL Notes </h1>






<br>

### Table of Content

- [Introduction to PostgreSQL](#introduction-to-postgresql)
- [🚧 Installation](#---installation)
  * [Install PostgreSQL in Ubuntu](#install-postgresql-in-ubuntu)
  * [Install PostgreSQL in CentOS](#install-postgresql-in-centos)
    + [Method 1: PostgreSQL Yum Repository](#method-1--postgresql-yum-repository)
    + [Method 2: Using DNF](#method-2--using-dnf)
- [Initial Configuration](#initial-configuration)
  * [Creating a New PostgreSQL Database Cluster](#creating-a-new-postgresql-database-cluster)
  * [Understanding PostgreSQL Roles and Databases](#understanding-postgresql-roles-and-databases)
  * [Creating a New Postgres Role](#creating-a-new-postgres-role)
- [Some Very First Commands](#some-very-first-commands)
  * [Examples](#examples)
  * [CREATE DATABASE](#create-database)
  * [DROP DATABASE](#drop-database)
  * [CREATE TABLE](#create-table)
  * [Table Definition](#table-definition)
  * [INSERT INTO](#insert-into)
  * [SELECT](#select)
  * [DROP TABLE](#drop-table)
- [The Surface Sea](#the-surface-sea)
  * [Mockaroo Data Generator](#mockaroo-data-generator)
  * [ORDER BY](#order-by)
    + [ASC](#asc)
    + [DESC](#desc)
    + [ORDER BY with Two-parameter](#order-by-with-two-parameter)
  * [DISTINCT](#distinct)
  * [WHERE](#where)
    + [BETWEEN](#between)
    + [LIKE](#like)
  * [GROUP BY](#group-by)
    + [GROUP BY with ORDER BY](#group-by-with-order-by)
    + [GROUP BY HAVING](#group-by-having)
  * [COALESCE](#coalesce)
  * [Another Table Called `car`](#another-table-called--car-)
  * [Basic Functions](#basic-functions)
    + [MAX](#max)
    + [MIN](#min)
    + [AVG](#avg)
    + [ROUND](#round)
    + [COUNT](#count)
    + [SUM](#sum)
  * [Basic Arithmetic Operations](#basic-arithmetic-operations)
  * [Discount Calculation](#discount-calculation)
  * [ALIAS](#alias)
  * [NULLIF](#nullif)
  * [DATE](#date)
    + [NOW](#now)
    + [Addition and Subtraction of Date](#addition-and-subtraction-of-date)
      - [INTERVAL](#interval)
    + [EXTRACT](#extract)
    + [AGE](#age)
- [The Shallow Sea](#the-shallow-sea)
  * [PRIMARY KEY](#primary-key)
  * [CONSTRAINTS](#constraints)
    + [UNIQUE constraint](#unique-constraint)
    + [CHECK Constraint](#check-constraint)
  * [DELETE](#delete)
  * [UPDATE](#update)
  * [ON CONFLICT](#on-conflict)
    + [DO NOTHING](#do-nothing)
    + [DO UPDATE SET](#do-update-set)
  * [Foreign Keys, Joins and Relationships](#foreign-keys--joins-and-relationships)
    + [Delete Record with Foreign Keys](#delete-record-with-foreign-keys)
  * [JOIN](#join)
    + [INNER JOIN](#inner-join)
    + [LEFT JOIN](#left-join)
    + [RIGHT JOIN](#right-join)
    + [FULL OUTER JOIN](#full-outer-join)
  * [Exporting Query Results to CSV](#exporting-query-results-to-csv)
  * [Serials and Sequences](#serials-and-sequences)
  * [Extensions](#extensions)
  * [UUID Datatype](#uuid-datatype)
    + [UUID as Primary Key](#uuid-as-primary-key)






<br>

# Introduction to PostgreSQL

PostgreSQL is a powerful, open source object-relational database system with over 30 years of active development that has earned it a strong reputation for reliability, feature robustness, and performance. 

PostgreSQL has earned a strong reputation for its proven architecture, reliability, data integrity, robust feature set, extensibility, and the dedication of the open source community behind the software to consistently deliver performant and innovative solutions. PostgreSQL runs on all major operating systems, including Linux, UNIX (AIX, BSD, HP-UX, SGI IRIX, Mac OS X, Solaris, Tru64), and Windows.


From Wikipedia:
> PostgreSQL (/ˈpoʊstɡrɛs ˌkjuː ˈɛl/), also known as Postgres, is a free and open-source relational database management system (RDBMS) emphasizing extensibility and SQL compliance. It was originally named POSTGRES, referring to its origins as a successor to the Ingres database developed at the University of California, Berkeley. In 1996, the project was renamed to PostgreSQL to reflect its support for SQL. After a review in 2007, the development team decided to keep the name PostgreSQL and the alias Postgres.




# 🚧 Installation


## Install PostgreSQL in Ubuntu

To install PostgreSQL in ubuntu, we have first to refresh our server’s local package index:
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

<sub>_Output_</sub>
```
postgresql                           9.6                             client, server [d]                          PostgreSQL server and client module                         
postgresql                           10 [d]                          client, server [d]                          PostgreSQL server and client module                         
postgresql                           12                              client, server                              PostgreSQL server and client module
```

In this output, we can see three versions of PostgreSQL available from the **AppStream** repository: `9.6`, `10`, and `12`. The stream that provides Postgres version 10 is the default, as indicated by the `[d]` following it. If we want to install that version, we could just run `sudo dnf install postgresql-server` and move on to the next step.

To install PostgreSQL version 12, we have to enable that version’s module stream. When we enable a module stream, we override the default stream and make all of the packages related to the enabled stream available on the system.

To enable the module stream for Postgres version 12, run the following command:
```
$ sudo dnf module enable postgresql:12
```

<sub>_Output_</sub>
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

After enabling the version 12 module stream, we can install the `postgresql-server` package to install PostgreSQL 12 and all of its dependencies:

```
$ sudo dnf install postgresql-server
```

When given the prompt, confirm the installation by pressing `y` then `ENTER`:

<sub>_Output_</sub>
```
. . .
Install  4 Packages

Total download size: 16 M
Installed size: 62 M
Is this ok [y/N]: y

```
Now PostgreSQL is installed, we have to perform some initialization steps to prepare a new database cluster for PostgreSQL.

See More:  [How To Install and Use PostgreSQL on CentOS 8](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-centos-8)



# Initial Configuration
## Creating a New PostgreSQL Database Cluster

We have to create a new PostgreSQL database cluster before we can start creating tables and loading them with data. A database cluster is a collection of databases that are managed by a single server instance. Creating a database cluster consists of creating the directories in which the database data will be placed, generating the shared catalog tables, and creating the `template1` and `postgres` databases.

The `template1` database is a template of sorts used to create new databases; everything that is stored in `template1`, even objects we add ourselves, will be placed in new databases when they’re created. The `postgres` database is a default database designed for use by users, utilities, and third-party applications.

The Postgres package we installed in the previous step comes with a handy script called `postgresql-setup` which helps with low-level database cluster administration.

To create a database cluster, run the script using `sudo` and with the `--initdb` option.


_If PostgreSQL is installed using the PostgreSQL Yum repository:_
```
$ /usr/pgsql-12/bin/postgresql-12-setup initdb
```

_If PostgreSQL is installed using DNF:_
```
$ sudo postgresql-setup --initdb
```

<sub>_Output_</sub>
```
 * Initializing database in '/var/lib/pgsql/data'
 * Initialized, logs are in /var/lib/pgsql/initdb_postgresql.log
```


Now start and enable PostgreSQL using `systemctl`.


_If PostgreSQL is installed using the PostgreSQL Yum repository:_

```
$ systemctl enable postgresql-12
$ systemctl start postgresql-12
```
_If PostgreSQL is installed using DNF:_
```
$ sudo systemctl start postgresql
$ sudo systemctl enable postgresql
```

<sub>_Output_</sub>
```
Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /usr/lib/systemd/system/postgresql.service.
```

Now that PostgreSQL is up and running, we will go over using roles to learn how Postgres works and how it is different from similar database management systems.




## Understanding PostgreSQL Roles and Databases


By default, Postgres uses a concept called roles to handle authentication and authorization. These are, in some ways, similar to regular Unix-style accounts, but Postgres does not distinguish between users and groups and instead prefers the more flexible term role.

Upon installation, Postgres is set up to use `ident` authentication, meaning that it associates Postgres roles with a matching Unix/Linux system account. If a role exists within Postgres, a Unix/Linux username with the same name can sign in as that role.

The installation procedure created a user account called `postgres` that is associated with the default Postgres role. To use Postgres, at first, we have to log in using that role.

So we have to switch over to the `postgres` UNIX user, which is created upon installation of Postgres, and then from the `postgres` UNIX user, we will able to log on Postgres server.
```
[arafat@server ~]$ sudo -i -u postgres
[postgres@server ~]$ psql
```

Alternatively, to access a Postgres prompt without switching users
```
[arafat@server ~]$ sudo -u postgres psql
```




## Creating a New Postgres Role


To log in with ident-based authentication, we will need a Linux user with the same name as our Postgres role and database.

If we don’t have a matching Linux user available, we must create one with the `adduser` command.
```
[arafat@server ~]$ sudo adduser postgresuser
```

We showed how to create a UNIX user named `postgresuser` here, but we will not use it. Instead, we will use the existing `arafat` user for a new Postgres roll.

Now we will create a Postgres role. After switching to `postgres` Linux user:
```
postgres@server:~$ createuser --interactive
Enter name of role to add: arafat
Shall the new role be a superuser? (y/n) y
```


Another assumption that the Postgres authentication system makes by default is that for any role used to log in, that role will have a database with the same name which it can access.

This means that if the user we created in the last section is called `arafat`, that role will attempt to connect to a database which is also called `arafat` by default. We can create such a database with the `createdb` command.

If we are logged in as the `postgres` user, we would type something like:

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





# Some Very First Commands


- `\q`: Quit/Exit
- `\l`:  List all databases in the current PostgreSQL database server
- `\h`: Show the list of SQL commands
- `\c __database__`:  Connect to a database
- `\d`: To describe (list) what tables are in the database
- `\d __table__`: Show table definition (columns, etc.) including triggers
- `\i __filename__`: to run (include) a script file of SQL commands
- `\w __filename__`: To write the last SQL command to a file
- `\h _command_`: Show syntax on this SQL command
- `\?`: Show the list of postgres commands



## Examples

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



## CREATE DATABASE
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


## DROP DATABASE
```
arafat_hasan=# DROP DATABASE test;
DROP DATABASE
```


## CREATE TABLE

We have to create a database again as we deleted the `test` database in the previous step. Here `\d` a list of tables, and there is no table in our newly created database.
```
arafat=# CREATE DATABASE test;
CREATE DATABASE
test=# \d
Did not find any relations.
```

Now here we are creating a new table named `person`:

<sub>_Command_</sub>
```sql
CREATE TABLE person (
 id BIGSERIAL NOT NULL PRIMARY KEY,
 first_name VARCHAR(50) NOT NULL,
 last_name VARCHAR(50) NOT NULL,
 gender VARCHAR(50) NOT NULL,
 date_of_birth DATE NOT NULL,
 email VARCHAR(150));
```

<sub>_Output_</sub>
```
CREATE TABLE
```

Now check the list of tables:
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



## Table Definition
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


## INSERT INTO
Notice that, as email is not `NOT NULL` so it is optional to insert into the table.

<sub>_Command_</sub>
```sql
INSERT INTO person (first_name, last_name, gender, date_of_birth)
 VALUES('Anne', 'Smith', 'female', DATE '1988-01-09');
```

<sub>_Output_</sub>
```
INSERT 0 1
```

<sub>_Command_</sub>
```sql
INSERT INTO person (first_name, last_name, gender, date_of_birth, email)
 VALUES('Jack', 'Doe', 'male', DATE '1985-11-03', 'jack@example.com');
```

<sub>_Output_</sub>
```
INSERT 0 1
```


## SELECT
Fetch all data from table:

<sub>_Command_</sub>
```sql
SELECT * FROM person;
```

<sub>_Output_</sub>
```
 id | first_name | last_name | gender | date_of_birth |      email       
----+------------+-----------+--------+---------------+------------------
  1 | Anne       | Smith     | female | 1988-01-09    | 
  2 | Jack       | Doe       | male   | 1985-11-03    | jack@example.com
(2 rows)
```


## DROP TABLE
Now we want to delete our table `person`.
```
test=# DROP TABLE person;
DROP TABLE
```



#  The Surface Sea


## Mockaroo Data Generator
We will use a site named [Mockaroo](https://mockaroo.com/) to insert a lot of data into our table for our learning convenience. Mockaroo is an online realist test data generator. We will download a bunch of dummy but realistic data in SQL format and execute the SQL file in the terminal.

![Generate data using Mockaroo](https://imgur.com/rLGnx8z.jpg, "Generate data using Mockaroo")

Notes:
- Field Names and types in Mockaroo according to the image above.
-  For our learning convenience, make sure 30% blank in the `email` field.
- Set a  acceptable nice range for data of birth.
- To find the type of each field, we have to search with the appropriate keyword.
- Tick the _include create table_ option.

Download the data as a file named `person.sql`.


Now we will do some tweaking in `person.sql`, according to our needs. Open this file in the preferred editor, I'm using vs code. Then make the following changes to the CREATE TABLE command at the top of the file.  Notice that we have added `id BIGSERIAL NOT NULL PRIMARY KEY`, changed `VARCHAR` sizes, and specified the `NOT NULL` fields.


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

After saving our changed `person.sql` file, now we will execute it. We can copy the whole file and paste it into Postgres terminal, that will work too, but we are going to do it in a better way. `\i` executes a script file of SQL commands.
```
test=# \i /path/to/person.sql 
```
Now the script `person.sql` is executed, and there are 1000 rows in the `person` table.

Our table description look like this:
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


Here we have made a query to fetch all the data from the `person` table.

<sub>_Command_</sub>
```sql
SELECT * FROM person;
```

<sub>_Output_</sub>
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

<sub>_Command_</sub>
```sql
SELECT id, first_name, last_name FROM person;
```

<sub>_Output_</sub>
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



### ASC
For ascending order:

<sub>_Command_</sub>
```sql
SELECT * FROM person ORDER BY country_of_birth;
```

<sub>_Output_</sub>
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


### DESC
For dscending order:

<sub>_Command_</sub>
```sql
SELECT * FROM person ORDER BY country_of_birth DESC;
```

<sub>_Output_</sub>
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
--More--

```

Date of birth in dscending order:

<sub>_Command_</sub>
```sql
SELECT * FROM person ORDER BY date_of_birth DESC;
```

<sub>_Output_</sub>

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



### ORDER BY with Two-parameter
This means that if `country_of_birth` is the same, then the rows will be sorted according to the `id` column. Check the difference with the previous one and this.

<sub>_Command_</sub>
```sql
SELECT * FROM person ORDER BY country_of_birth, id;
```

<sub>_Output_</sub>
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

## DISTINCT
The `SELECT DISTINCT` statement is used to return only distinct (different) values.

<sub>_Command_</sub>
```sql
SELECT DISTINCT country_of_birth FROM person ORDER BY country_of_birth;
```

<sub>_Output_</sub>
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
## WHERE
The `WHERE` clause is used to extract only those records that fulfill a specified condition.

<sub>_Command_</sub>
```sql
SELECT * FROM person WHERE gender='Female';
```

<sub>_Output_</sub>
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



### BETWEEN
The `BETWEEN` operator selects values within a given range. The values can be numbers, text, or dates.

The `BETWEEN` operator is inclusive: begin and end values are included.

<sub>_Command_</sub>
```sql
SELECT * FROM person WHERE date_of_birth BETWEEN '1985-02-02' AND '1986-06-04';
```

<sub>_Output_</sub>
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

### LIKE
The `LIKE` operator is used in a `WHERE` clause to search for a specified pattern in a column.

There are two wildcards often used in conjunction with the LIKE operator:

- `%`: The percent sign represents zero, one, or multiple characters
- `_`: The underscore represents a single character

Find all emails ending with `disqus.com`:

<sub>_Command_</sub>
```sql
SELECT * FROM person WHERE email LIKE '%disqus.com';
```

<sub>_Output_</sub>
```
 id  | first_name |  last_name   |           email            | gender | date_of_birth | country_of_birth 
-----+------------+--------------+----------------------------+--------+---------------+------------------
  45 | Carleen    | Dzeniskevich | cdzeniskevich18@disqus.com | Female | 1985-06-18    | China
 852 | Alex       | Garmans      | agarmansnn@disqus.com      | Male   | 1990-11-08    | China
(2 rows)
```


## GROUP BY
The `GROUP BY` statement groups rows that have the same values into summary rows, like "find the number of persons in each country".

The `GROUP BY` statement is often used with aggregate functions (`COUNT`, `MAX`, `MIN`, `SUM`, `AVG`) to group the result-set by one or more columns.

<sub>_Command_</sub>
```sql
SELECT country_of_birth, COUNT(*) FROM person GROUP BY country_of_birth;
```

<sub>_Output_</sub>

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
### GROUP BY with ORDER BY

<sub>_Command_</sub>
```sql
SELECT country_of_birth, COUNT(*) FROM person GROUP BY country_of_birth ORDER BY country_of_birth;
```

<sub>_Output_</sub>
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

### GROUP BY HAVING
The HAVING clause was added to SQL because the WHERE keyword could not be used with aggregate functions.

<sub>_Command_</sub>
```sql
SELECT country_of_birth, COUNT(*) FROM person GROUP BY country_of_birth HAVING COUNT(*) > 50 ORDER BY country_of_birth;
```

<sub>_Output_</sub>
```
 country_of_birth | count 
------------------+-------
 China            |   180
 Indonesia        |   109
 Russia           |    56
(3 rows)
```

## COALESCE
The `COALESCE()` function returns the first non-null value in a list.

<sub>_Command_</sub>
```sql
SELECT COALESCE(email, 'Email not provided') FROM person;
```

<sub>_Output_</sub>
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

## Another Table Called `car`
Now we will download a new bunch of data to create another table called `car`. This table has these columns:
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

<sub>_Command_</sub>
```sql
SELECT * FROM car LIMIT 10;
```

<sub>_Output_</sub>
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


## Basic Functions

### MAX

The `MAX()` function returns the largest value of the selected column.

<sub>_Command_</sub>
```sql
SELECT MAX(price) FROM car;
```

<sub>_Output_</sub>
```
    max    
-----------
 299959.83
(1 row)
```

<sub>_Command_</sub>
```sql
SELECT make, MAX(price) FROM car GROUP BY make LIMIT 5;
```

<sub>_Output_</sub>
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

### MIN
The `MIN()` function returns the smallest value of the selected column.

<sub>_Command_</sub>
```sql
SELECT MIN(price) FROM car;
```

<sub>_Output_</sub>
```
   min    
----------
 30348.16
(1 row)
```

<sub>_Command_</sub>
```sql
SELECT make, MIN(price) FROM car GROUP BY make LIMIT 5;
```

<sub>_Output_</sub>
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

### AVG
The `AVG()` function returns the average value of a numeric column.

<sub>_Command_</sub>
```sql
SELECT AVG(price) FROM car;
```

<sub>_Output_</sub>
```
         avg         
---------------------
 164735.601300000000
(1 row)
```

<sub>_Command_</sub>
```sql
SELECT make, AVG(price) FROM car GROUP BY make LIMIT 5;
```

<sub>_Output_</sub>
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


### ROUND
The PostgreSQL `ROUND()` function rounds a numeric value to its nearest integer or a number with the number of decimal places.

<sub>_Command_</sub>
```sql
SELECT ROUND(AVG(price)) FROM car;
```

<sub>_Output_</sub>
```
 round  
--------
 164736
(1 row)
```

<sub>_Command_</sub>
```sql
SELECT make, ROUND(AVG(price)) FROM car GROUP BY make LIMIT 5;
```

<sub>_Output_</sub>
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

### COUNT
The `COUNT()` function returns the number of rows that match a specified criterion.

<sub>_Command_</sub>
```sql
SELECT COUNT(make) FROM car;
```

<sub>_Output_</sub>
```
 count 
-------
  1000
(1 row)
```

### SUM
The `SUM()` function returns the total sum of a numeric column.

<sub>_Command_</sub>
```sql
SELECT SUM(price) FROM car;
```

<sub>_Output_</sub>
```
     sum      
--------------
 164735601.30
(1 row)
```

<sub>_Command_</sub>
```sql
SELECT make, SUM(price) FROM car GROUP BY make LIMIT 5;
```

<sub>_Output_</sub>
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

## Basic Arithmetic Operations

<sub>_Command_</sub>
```sql
SELECT 10 + 2;
```

<sub>_Output_</sub>
```
 ?column? 
----------
       12
(1 row)
```

<sub>_Command_</sub>
```sql
SELECT 10 / 2;
```

<sub>_Output_</sub>
```
 ?column? 
----------
        5
(1 row)
```

<sub>_Command_</sub>
```sql
SELECT 10^2;
```

<sub>_Output_</sub>
```
 ?column? 
----------
      100
(1 row)
```

## Discount Calculation
Now suppose the company offers a 10% discount on all cars. We will now calculate the amount of this 10%, and calculate the new price.

<sub>_Command_</sub>
```sql
SELECT id, make, model, price, ROUND(price * 0.10, 2), ROUND(price - (price * 0.10), 2) FROM car;
```

<sub>_Output_</sub>
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



## ALIAS

SQL aliases are used to give a table, or a column in a table, a temporary name. Aliases are often used to make column names more readable. An alias only exists for the duration of the query.


<sub>_Command_</sub>
```sql
SELECT id, make, model, price AS original_price,
 ROUND(price * 0.10, 2) AS ten_percent_discount,
 ROUND(price - (price * 0.10), 2) AS discounted_price
 FROM car;
```

<sub>_Output_</sub>
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


## NULLIF
The NULLIF() function returns NULL if two expressions are equal. Otherwise, it returns the first expression.

```
test=# SELECT NULLIF(2, 1);
 nullif 
--------
      2
(1 row)

test=# SELECT NULLIF('a', 'b');
 nullif 
--------
 a
(1 row)

test=# SELECT NULLIF(0, 0);
 nullif 
--------
       
(1 row)
	
```



## DATE
PostgreSQL provides several functions that return values related to the current date and time. These SQL-standard functions all return values based on the start time of the current transaction:

```sql
CURRENT_DATE
CURRENT_TIME
CURRENT_TIMESTAMP
CURRENT_TIME(precision)

```


```
SELECT CURRENT_TIME;
Result: 14:39:53.662522-05

SELECT CURRENT_DATE;
Result: 2001-12-23

SELECT CURRENT_TIMESTAMP;
Result: 2001-12-23 14:39:53.662522-05
```


PostgreSQL also provides functions that return the start time of the current statement, as well as the actual current time at the instant the function is called. The complete list of non-SQL-standard time functions is:

```sql
transaction_timestamp()
statement_timestamp()
clock_timestamp()
timeofday()
now()

```

### NOW

```
test=# SELECT NOW();
             now              
------------------------------
 2020-08-19 23:39:49.18778+06
(1 row)

test=# SELECT NOW()::DATE;
    now     
------------
 2020-08-19
(1 row)

test=# SELECT NOW()::TIME;
       now       
-----------------
 23:40:44.645625
(1 row)

```

### Addition and Subtraction of Date
#### INTERVAL

```
test=# SELECT NOW() - INTERVAL '1 YEAR';
           ?column?            
-------------------------------
 2019-08-19 23:47:11.475305+06
(1 row)

test=# SELECT NOW() - INTERVAL '10 YEAR';
           ?column?            
-------------------------------
 2010-08-19 23:47:31.627347+06
(1 row)

test=# SELECT NOW() - INTERVAL '3 MONTHS';
           ?column?            
-------------------------------
 2020-05-19 23:47:53.403383+06
(1 row)

test=# SELECT NOW() + INTERVAL '40 DAYS';
           ?column?            
-------------------------------
 2020-09-28 23:48:31.419856+06
(1 row)


```



### EXTRACT
The extract function retrieves subfields such as year or hour from date/time values. *source* must be a value expression of type `timestamp`, `time`, or `interval`. (Expressions of type date are cast to `timestamp` and can, therefore, be used as well.) *field* is an identifier or string that selects what field to extract from the source value. The extract function returns values of type double precision. 

```
EXTRACT(field FROM source)
```


```
test=# SELECT NOW();
             now              
------------------------------
 2020-08-19 23:55:42.13778+06
(1 row)

test=# SELECT EXTRACT(YEAR FROM NOW());
 date_part 
-----------
      2020
(1 row)

test=# SELECT EXTRACT(MONTH FROM NOW());
 date_part 
-----------
         8
(1 row)

test=# SELECT EXTRACT(CENTURY FROM NOW());
 date_part 
-----------
        21
(1 row)


```


### AGE

```
age(timestamp, timestamp)
```
or
```
age(timestamp)
```
The return type of both is an interval.

<sub>_Command_</sub>
```sql
SELECT first_name, last_name, gender, date_of_birth, AGE(NOW(), date_of_birth) AS age FROM person;
```

<sub>_Output_</sub>
```
   first_name   |      last_name      | gender | date_of_birth |                   age                    
----------------+---------------------+--------+---------------+------------------------------------------
 Ronda          | Skermer             | Female | 1993-06-30    | 27 years 1 mon 19 days 23:56:04.414053
 Hamid          | Abbett              | Male   | 1995-08-31    | 24 years 11 mons 19 days 23:56:04.414053
 Francis        | Nickerson           | Male   | 1998-03-16    | 22 years 5 mons 3 days 23:56:04.414053
 Erminie        | M'Quharg            | Female | 1999-03-13    | 21 years 5 mons 6 days 23:56:04.414053
 Teodoro        | Trimmill            | Male   | 1982-04-30    | 38 years 3 mons 19 days 23:56:04.414053
 Reilly         | Amesbury            | Male   | 1990-12-31    | 29 years 7 mons 19 days 23:56:04.414053
 West           | Elphey              | Male   | 2004-03-29    | 16 years 4 mons 21 days 23:56:04.414053
 Letta          | Caurah              | Female | 1994-09-09    | 25 years 11 mons 10 days 23:56:04.414053
 Elset          | Agass               | Female | 2004-06-26    | 16 years 1 mon 23 days 23:56:04.414053
--More--
```

See More: [Date/Time Types](https://www.postgresql.org/docs/9.1/datatype-datetime.html)



# The Shallow Sea

## PRIMARY KEY
The `PRIMARY KEY` of a table is a combination of `NOT NULL` and `UNIQUE` constraint. 
Here we will see how to delete and add a primary key.


At first, we check the table description, and we have found that the `id` column is a `PRIMARY KEY`.

```
\d person;
```

```
                                         Table "public.person"
      Column      |          Type          | Collation | Nullable |              Default               
------------------+------------------------+-----------+----------+------------------------------------
 id               | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id)
    
```

Now we will try to add a duplicate value to the table.

```
test=# SELECT * FROM person WHERE id=1;
 id | first_name | last_name |           email           | gender | date_of_birth | country_of_birth 
----+------------+-----------+---------------------------+--------+---------------+------------------
  1 | Ronda      | Skermer   | rskermer0@arstechnica.com | Female | 1993-06-30    | Argentina
(1 row)

test=# INSERT INTO person (id, first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES (1, 'Ronda', 'Skermer', 'rskermer0@arstechnica.com', 'Female', '1993-06-30', 'Argentina');
ERROR:  duplicate key value violates unique constraint "person_pkey"
DETAIL:  Key (id)=(1) already exists.
```


Insertion value is failed as the `id` column is primary, and it says _duplicate key value violates unique constraint_. Now we will drop the primary key constraint of the `id` column and will again try to insert duplicate data into the table.

```
test=# ALTER TABLE person DROP CONSTRAINT person_pkey;
ALTER TABLE
test=# \d person;
                                         Table "public.person"
      Column      |          Type          | Collation | Nullable |              Default               
------------------+------------------------+-----------+----------+------------------------------------
 id               | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 

test=# INSERT INTO person (id, first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES (1, 'Ronda', 'Skermer', 'rskermer0@arstechnica.com', 'Female', '1993-06-30', 'Argentina');
INSERT 0 1
test=# SELECT * FROM person WHERE id=1;
 id | first_name | last_name |           email           | gender | date_of_birth | country_of_birth 
----+------------+-----------+---------------------------+--------+---------------+------------------
  1 | Ronda      | Skermer   | rskermer0@arstechnica.com | Female | 1993-06-30    | Argentina
  1 | Ronda      | Skermer   | rskermer0@arstechnica.com | Female | 1993-06-30    | Argentina
(2 rows)
```

Here, as we can see that, after dropping the primary key constrains, we can insert a duplicate row in the table.

Now we will try to add primary key constraint in the `id` column.	

```
test=# ALTER TABLE person ADD PRIMARY KEY(id);
ERROR:  could not create unique index "person_pkey"
DETAIL:  Key (id)=(1) is duplicated.
```

But we had failed, as there is two-row containing the same id. Now delete one of the duplicate ids and again try to add a primary key.

```
test=# DELETE FROM person WHERE id=1;
DELETE 2
test=# SELECT * FROM person WHERE id=1;
 id | first_name | last_name | email | gender | date_of_birth | country_of_birth 
----+------------+-----------+-------+--------+---------------+------------------
(0 rows)

test=# INSERT INTO person (id, first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES (1, 'Ronda', 'Skermer', 'rskermer0@arstechnica.com', 'Female', '1993-06-30', 'Argentina');
INSERT 0 1
test=# SELECT * FROM person WHERE id=1;
 id | first_name | last_name |           email           | gender | date_of_birth | country_of_birth 
----+------------+-----------+---------------------------+--------+---------------+------------------
  1 | Ronda      | Skermer   | rskermer0@arstechnica.com | Female | 1993-06-30    | Argentina
(1 row)

test=# ALTER TABLE person ADD PRIMARY KEY(id);
ALTER TABLE
test=# \d person;
                                         Table "public.person"
      Column      |          Type          | Collation | Nullable |              Default               
------------------+------------------------+-----------+----------+------------------------------------
 id               | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id)

test=# 

```
Our primary key constraint in the `id` column is back again.


## CONSTRAINTS
### UNIQUE constraint
The PostgreSQL `UNIQUE` constraint ensures that the uniqueness of the values entered into a column or a field of a table.

The `UNIQUE` constraint in PostgreSQL can be applied as a column constraint or a group of column constraint or a table constraint.

The `UNIQUE` constraint in PostgreSQL is violated when more than one row for a column or combination of columns which have been used as a unique constraint in a table. Two `NULL` values for a column in different rows are different, and it does not violate the uniqueness of the UNIQUE constraint.

When a `UNIQUE` constraint is adding, an index on a column or group of columns creates automatically.


We are going to add a `UNIQUE CONSTRAINT` in the email field, and after that, we will delete the constraint of the field.

```
test=# ALTER TABLE person ADD CONSTRAINT unique_email_addr UNIQUE(email);
ALTER TABLE
test=# \d person;
                                         Table "public.person"
      Column      |          Type          | Collation | Nullable |              Default               
------------------+------------------------+-----------+----------+------------------------------------
 id               | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id)
    "unique_email_addr" UNIQUE CONSTRAINT, btree (email)

test=# ALTER TABLE person DROP CONSTRAINT unique_email_addr;
ALTER TABLE

test=# \d person;
                                         Table "public.person"
      Column      |          Type          | Collation | Nullable |              Default               
------------------+------------------------+-----------+----------+------------------------------------
 id               | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id)

```

Again we will add unique constraints in the email field, but without mentioning the name of our constraint, the name of the constraint will be set by Postgres itself automatically.

```
test=# ALTER TABLE person ADD UNIQUE(email);
ALTER TABLE
test=# \d person;
                                         Table "public.person"
      Column      |          Type          | Collation | Nullable |              Default               
------------------+------------------------+-----------+----------+------------------------------------
 id               | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id)
    "person_email_key" UNIQUE CONSTRAINT, btree (email)

```

### CHECK Constraint
The PostgreSQL `CHECK` constraint controls the value of a column(s) being inserted.

PostgreSQL provides the `CHECK` constraint, which allows the user to define a condition that a value entered into a table, has to satisfy before it can be accepted. The `CHECK` constraint consists of the keyword `CHECK`, followed by parenthesized conditions. The attempt will be rejected when update or insert column values that will make the condition false.

The `CHECK` constraint in PostgreSQL can be defined as a separate name.


```
test=# ALTER TABLE person ADD CONSTRAINT gender_constraint CHECK (gender = 'Female' OR gender = 'Male');
ALTER TABLE
test=# \d person;
                                         Table "public.person"
      Column      |          Type          | Collation | Nullable |              Default               
------------------+------------------------+-----------+----------+------------------------------------
 id               | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id)
    "person_email_key" UNIQUE CONSTRAINT, btree (email)
Check constraints:
    "gender_constraint" CHECK (gender::text = 'Female'::text OR gender::text = 'Male'::text)

```

## DELETE
Following is the usage of the PostgreSQL `DELETE` command to delete data of a PostgreSQL table.

```
DELETE FROM table_name ;
```

Where `table_name` is the associated table, executing this command will delete all the rows of the associated table.

```
DELETE FROM table_name WHERE condition;
```

If we don't want to delete all of the rows of a table, but some specific rows which match the "condition", execute the above.


First, try to delete all records from a table.

```
test=# DELETE FROM person;
DELETE 1000
test=# SELECT * FROM person;
 id | first_name | last_name | email | gender | date_of_birth | country_of_birth 
----+------------+-----------+-------+--------+---------------+------------------
(0 rows)

```

There is no record in the `person` table now. For our learning purpose, retrieve data from the SQL file for the table again.

```
test=# \i /path/to/person.sql 
psql:/path/to/person.sql:9: ERROR:  relation "person" already exists
INSERT 0 1
--More--
test=# \d person;
                                         Table "public.person"
      Column      |          Type          | Collation | Nullable |              Default               
------------------+------------------------+-----------+----------+------------------------------------
 id               | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id)
    "person_email_key" UNIQUE CONSTRAINT, btree (email)
Check constraints:
    "gender_constraint" CHECK (gender::text = 'Female'::text OR gender::text = 'Male'::text)

test=# SELECT * FROM person LIMIT 10;
  id  | first_name |   last_name   |             email              | gender | date_of_birth | country_of_birth 
------+------------+---------------+--------------------------------+--------+---------------+------------------
 1002 | Ronda      | Skermer       | rskermer0@arstechnica.com      | Female | 1993-06-30    | Argentina
 1003 | Hamid      | Abbett        | habbett1@cbc.ca                | Male   | 1995-08-31    | Ethiopia
 1004 | Francis    | Nickerson     | fnickerson2@mac.com            | Male   | 1998-03-16    | Portugal
 1005 | Erminie    | M'Quharg      | emquharg3@e-recht24.de         | Female | 1999-03-13    | Mozambique
 1006 | Teodoro    | Trimmill      |                                | Male   | 1982-04-30    | China
 1007 | Reilly     | Amesbury      | ramesbury5@businessinsider.com | Male   | 1990-12-31    | China
 1008 | West       | Elphey        |                                | Male   | 2004-03-29    | Indonesia
 1009 | Letta      | Caurah        | lcaurah7@yale.edu              | Female | 1994-09-09    | Indonesia
 1010 | Elset      | Agass         | eagass8@rambler.ru             | Female | 2004-06-26    | China
 1011 | Aurore     | Drillingcourt | adrillingcourt9@cnet.com       | Female | 1977-10-19    | China
(10 rows)
```

Now try to delete a specific row or rows with the matching condition.

```
test=# DELETE FROM person WHERE id = 1002;
DELETE 1
test=# SELECT * FROM person LIMIT 10;
  id  | first_name |   last_name   |             email              | gender | date_of_birth | country_of_birth 
------+------------+---------------+--------------------------------+--------+---------------+------------------
 1003 | Hamid      | Abbett        | habbett1@cbc.ca                | Male   | 1995-08-31    | Ethiopia
 1004 | Francis    | Nickerson     | fnickerson2@mac.com            | Male   | 1998-03-16    | Portugal
 1005 | Erminie    | M'Quharg      | emquharg3@e-recht24.de         | Female | 1999-03-13    | Mozambique
 1006 | Teodoro    | Trimmill      |                                | Male   | 1982-04-30    | China
 1007 | Reilly     | Amesbury      | ramesbury5@businessinsider.com | Male   | 1990-12-31    | China
 1008 | West       | Elphey        |                                | Male   | 2004-03-29    | Indonesia
 1009 | Letta      | Caurah        | lcaurah7@yale.edu              | Female | 1994-09-09    | Indonesia
 1010 | Elset      | Agass         | eagass8@rambler.ru             | Female | 2004-06-26    | China
 1011 | Aurore     | Drillingcourt | adrillingcourt9@cnet.com       | Female | 1977-10-19    | China
 1012 | Ilse       | Goldman       | igoldmana@ihg.com              | Female | 2001-07-31    | Mongolia
(10 rows)

test=# DELETE FROM person WHERE gender='Female' AND country_of_birth='China';
DELETE 94
test=# SELECT * FROM person WHERE gender='Female' AND country_of_birth='China';
 id | first_name | last_name | email | gender | date_of_birth | country_of_birth 
----+------------+-----------+-------+--------+---------------+------------------
(0 rows)

```

For our learning purpose, now we will delete every record from the person table and restore it from our SQL file.

```
test=# DELETE FROM person;
DELETE 905
test=# \i /path/to/person.sql
psql:/path/to/person.sql:9: ERROR:  relation "person" already exists
INSERT 0 1
--More--
```


## UPDATE
UPDATE command is used to modify existing data of a table. 

```
test=# SELECT * FROM person;
  id  |   first_name   |      last_name      |                  email                  | gender | date_of_birth |         country_of_birth         
------+----------------+---------------------+-----------------------------------------+--------+---------------+----------------------------------
 2002 | Ronda          | Skermer             | rskermer0@arstechnica.com               | Female | 1993-06-30    | Argentina
 2003 | Hamid          | Abbett              | habbett1@cbc.ca                         | Male   | 1995-08-31    | Ethiopia
 2004 | Francis        | Nickerson           | fnickerson2@mac.com                     | Male   | 1998-03-16    | Portugal
 2005 | Erminie        | M'Quharg            | emquharg3@e-recht24.de                  | Female | 1999-03-13    | Mozambique
 2006 | Teodoro        | Trimmill            |                                         | Male   | 1982-04-30    | China
 2007 | Reilly         | Amesbury            | ramesbury5@businessinsider.com          | Male   | 1990-12-31    | China
 2008 | West           | Elphey              |                                         | Male   | 2004-03-29    | Indonesia
--More--

test=# UPDATE person SET email  = 'teodoro@gmail.com' WHERE id = 2006;
UPDATE 1
test=# SELECT * FROM person WHERE id = 2006;
  id  | first_name | last_name |       email       | gender | date_of_birth | country_of_birth 
------+------------+-----------+-------------------+--------+---------------+------------------
 2006 | Teodoro    | Trimmill  | teodoro@gmail.com | Male   | 1982-04-30    | China
(1 row)

test=# UPDATE person SET last_name = 'Trimmil', email = 'teodoro@hotmail.com' WHERE id = 2006;
UPDATE 1
test=# SELECT * FROM person WHERE id = 2006;
  id  | first_name | last_name |        email        | gender | date_of_birth | country_of_birth 
------+------------+-----------+---------------------+--------+---------------+------------------
 2006 | Teodoro    | Trimmil   | teodoro@hotmail.com | Male   | 1982-04-30    | China
(1 row)


```

## ON CONFLICT
### DO NOTHING
This means do nothing if the row already exists in the table. It handles duplicate key errors.


First, we try to enter the duplicate record.

<sub>_Command_</sub>
```sql
INSERT INTO person (id, first_name, last_name, gender, email, date_of_birth, country_of_birth)
VALUES (2002, 'Ronda', 'Dante', 'Male', 'dante@hotmaill.com', DATE '1980-03-12', 'Sri Lanka');
```

As expected, an ERROR message is thrown.

<sub>_Output_</sub>
```
ERROR:  duplicate key value violates unique constraint "person_pkey"
DETAIL:  Key (id)=(2002) already exists.
```

Now we try to enter the duplicate record with `ON CONFLICT(id) DO NOTHING` and handle the error.

<sub>_Command_</sub>
```sql
INSERT INTO person (id, first_name, last_name, gender, email, date_of_birth, country_of_birth)
VALUES (2002, 'Ronda', 'Dante', 'Male', 'dante@hotmaill.com', DATE '1980-03-12', 'Sri Lanka')
ON CONFLICT(id) DO NOTHING;
```

The output message is saying `0 0`, which means no insert operation is held.

<sub>_Output_</sub>
```
INSERT 0 0
```

### DO UPDATE SET
This update some fields in the table.

We will update this record in a way that conflicts with it.

```
test=# SELECT * FROM person WHERE id = 2002;
  id  | first_name | last_name |             email         | gender | date_of_birth | country_of_birth 
------+------------+-----------+---------------------------+--------+---------------+------------------
 2002 | Ronda      | Skermer   | rskermer0@arstechnica.com | Female | 1993-06-30    | Argentina
(1 row)

```

Here `EXCLUDED` refers to the new conflicted record which is trying to be inserted.

<sub>_Command_</sub>
```sql
INSERT INTO person (id, first_name, last_name, gender, email, date_of_birth, country_of_birth)
VALUES (2002, 'Rudi', 'Donte', 'Male', 'donte@hotmaill.com', DATE '1980-03-12', 'Sri Lanka')
ON CONFLICT(id) DO UPDATE SET first_name=EXCLUDED.first_name, last_name=EXCLUDED.last_name, email=EXCLUDED.email;
```

<sub>_Output_</sub>
```
INSERT 0 1
```

Despite the conflict, the updated record is:

```
test=# SELECT * FROM person WHERE id = 2002;
  id  | first_name | last_name |       email        | gender | date_of_birth | country_of_birth 
------+------------+-----------+--------------------+--------+---------------+------------------
 2002 | Rudi       | Donte     | donte@hotmaill.com | Female | 1993-06-30    | Argentina
(1 row)

```


## Foreign Keys, Joins and Relationships
![Forign Key, Primary Key and Relations](https://imgur.com/N0Qcfe8.jpg, "Forign Key, Primary Key and Relations")


Adding relations between tables
We will now drop the previous tables and create new ones with relations.

```
test=# \dt
           List of relations
 Schema |  Name  | Type  |    Owner     
--------+--------+-------+--------------
 public | car    | table | arafat_hasan
 public | person | table | arafat_hasan
(2 rows)

test=# DROP TABLE car;
DROP TABLE
test=# DROP TABLE person;
DROP TABLE
test=# \dt
Did not find any relations.  
test=# \i /path/to/new/file/car-person.sql 
CREATE TABLE
CREATE TABLE
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
INSERT 0 1
test=# \dt
           List of relations
 Schema |  Name  | Type  |    Owner     
--------+--------+-------+--------------
 public | car    | table | arafat_hasan
 public | person | table | arafat_hasan
(2 rows)

```


Our new SQL file, which is named `car-person.sql` is in bellow:

```sql
CREATE TABLE car (
	id BIGSERIAL NOT NULL PRIMARY KEY,
	make VARCHAR(100) NOT NULL,
	model VARCHAR(100) NOT NULL,
	price NUMERIC(19, 2) NOT NULL
);


CREATE TABLE person (
	id BIGSERIAL NOT NULL PRIMARY KEY,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(50) NOT NULL,
	email VARCHAR(150),
	gender VARCHAR(7) NOT NULL,
	date_of_birth DATE NOT NULL,
	country_of_birth VARCHAR(50) NOT NULL,
	car_id BIGINT REFERENCES car(id),
	UNIQUE(car_id)
);


INSERT INTO car (make, model, price) VALUES ('Daewoo', 'Leganza', '241058.40');
INSERT INTO car (make, model, price) VALUES ('Mitsubishi', 'Montero', '269595.21');
INSERT INTO car (make, model, price) VALUES ('Kia', 'Rio', '245275.16');
INSERT INTO car (make, model, price) VALUES ('Jaguar', 'X-Type', '41665.96');
INSERT INTO car (make, model, price) VALUES ('Lincoln', 'Mark VIII', '163843.38');
INSERT INTO car (make, model, price) VALUES ('GMC', 'Rally Wagon 3500', '231169.05');
INSERT INTO car (make, model, price) VALUES ('Cadillac', 'Escalade ESV', '279951.34');


INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Hamid', 'Abbett', 'habbett1@cbc.ca', 'Male', '1995-08-31', 'Ethiopia');
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Francis', 'Nickerson', 'fnickerson2@mac.com', 'Male', '1998-03-16', 'Portugal');
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Erminie', 'M''Quharg', 'emquharg3@e-recht24.de', 'Female', '1999-03-13', 'Mozambique');
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Teodoro', 'Trimmill', null, 'Male', '1982-04-30', 'China');
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Reilly', 'Amesbury', 'ramesbury5@businessinsider.com', 'Male', '1990-12-31', 'China');
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('West', 'Elphey', null, 'Male', '2004-03-29', 'Indonesia');
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Letta', 'Caurah', 'lcaurah7@yale.edu', 'Female', '1994-09-09', 'Indonesia');
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Elset', 'Agass', 'eagass8@rambler.ru', 'Female', '2004-06-26', 'China');
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Aurore', 'Drillingcourt', 'adrillingcourt9@cnet.com', 'Female', '1977-10-19', 'China');
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Ilse', 'Goldman', 'igoldmana@ihg.com', 'Female', '2001-07-31', 'Mongolia');

```


Let's take a look at the two new tables to see what's inside.

```
test=# SELECT * FROM person;
 id | first_name |   last_name   |             email              | gender | date_of_birth | country_of_birth | car_id 
----+------------+---------------+--------------------------------+--------+---------------+------------------+--------
  1 | Hamid      | Abbett        | habbett1@cbc.ca                | Male   | 1995-08-31    | Ethiopia         |       
  2 | Francis    | Nickerson     | fnickerson2@mac.com            | Male   | 1998-03-16    | Portugal         |       
  3 | Erminie    | M'Quharg      | emquharg3@e-recht24.de         | Female | 1999-03-13    | Mozambique       |       
  4 | Teodoro    | Trimmill      |                                | Male   | 1982-04-30    | China            |       
  5 | Reilly     | Amesbury      | ramesbury5@businessinsider.com | Male   | 1990-12-31    | China            |       
  6 | West       | Elphey        |                                | Male   | 2004-03-29    | Indonesia        |       
  7 | Letta      | Caurah        | lcaurah7@yale.edu              | Female | 1994-09-09    | Indonesia        |       
  8 | Elset      | Agass         | eagass8@rambler.ru             | Female | 2004-06-26    | China            |       
  9 | Aurore     | Drillingcourt | adrillingcourt9@cnet.com       | Female | 1977-10-19    | China            |       
 10 | Ilse       | Goldman       | igoldmana@ihg.com              | Female | 2001-07-31    | Mongolia         |       
(10 rows)

test=# SELECT * FROM car;
 id |    make    |      model       |   price   
----+------------+------------------+-----------
  1 | Daewoo     | Leganza          | 241058.40
  2 | Mitsubishi | Montero          | 269595.21
  3 | Kia        | Rio              | 245275.16
  4 | Jaguar     | X-Type           |  41665.96
  5 | Lincoln    | Mark VIII        | 163843.38
  6 | GMC        | Rally Wagon 3500 | 231169.05
  7 | Cadillac   | Escalade ESV     | 279951.34
(7 rows)

```


As expected, there is no value in the `car_id` column in `person` as we did not insert any value there.

As can be seen below, we have set the foreign key correctly, and it has a UNIQUE constraint and `car_id` referencing to `car.id`.

```
test=# \d person;
                                         Table "public.person"
      Column      |          Type          | Collation | Nullable |              Default               
------------------+------------------------+-----------+----------+------------------------------------
 id               | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 
 car_id           | bigint                 |           |          | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id)
    "person_car_id_key" UNIQUE CONSTRAINT, btree (car_id)
Foreign-key constraints:
    "person_car_id_fkey" FOREIGN KEY (car_id) REFERENCES car(id)


```


Let's assign the Mitsubishi, which ID is 2 from the car table to Hamid Abbett of the person table which ID is 1.

```
test=# UPDATE person SET car_id = 2 WHERE id = 1;
UPDATE 1
test=# SELECT * FROM person;
 id | first_name |   last_name   |             email              | gender | date_of_birth | country_of_birth | car_id 
----+------------+---------------+--------------------------------+--------+---------------+------------------+--------
  2 | Francis    | Nickerson     | fnickerson2@mac.com            | Male   | 1998-03-16    | Portugal         |       
  3 | Erminie    | M'Quharg      | emquharg3@e-recht24.de         | Female | 1999-03-13    | Mozambique       |       
  4 | Teodoro    | Trimmill      |                                | Male   | 1982-04-30    | China            |       
  5 | Reilly     | Amesbury      | ramesbury5@businessinsider.com | Male   | 1990-12-31    | China            |       
  6 | West       | Elphey        |                                | Male   | 2004-03-29    | Indonesia        |       
  7 | Letta      | Caurah        | lcaurah7@yale.edu              | Female | 1994-09-09    | Indonesia        |       
  8 | Elset      | Agass         | eagass8@rambler.ru             | Female | 2004-06-26    | China            |       
  9 | Aurore     | Drillingcourt | adrillingcourt9@cnet.com       | Female | 1977-10-19    | China            |       
 10 | Ilse       | Goldman       | igoldmana@ihg.com              | Female | 2001-07-31    | Mongolia         |       
  1 | Hamid      | Abbett        | habbett1@cbc.ca                | Male   | 1995-08-31    | Ethiopia         |      2
(10 rows)

```

Let's also add a car to Francis Nickerson.

```
UPDATE person SET car_id = 1 WHERE id = 2;
```

Let's try to give one car to two people and see what happens.

```
test=# UPDATE person SET car_id = 1 WHERE id = 3;
ERROR:  duplicate key value violates unique constraint "person_car_id_key"
DETAIL:  Key (car_id)=(1) already exists.
```

Okay, now assign other cars to specific persons. This is the final table.

```
 id | first_name |   last_name   |             email              | gender | date_of_birth | country_of_birth | car_id 
----+------------+---------------+--------------------------------+--------+---------------+------------------+--------
  5 | Reilly     | Amesbury      | ramesbury5@businessinsider.com | Male   | 1990-12-31    | China            |       
  9 | Aurore     | Drillingcourt | adrillingcourt9@cnet.com       | Female | 1977-10-19    | China            |       
 10 | Ilse       | Goldman       | igoldmana@ihg.com              | Female | 2001-07-31    | Mongolia         |       
  1 | Hamid      | Abbett        | habbett1@cbc.ca                | Male   | 1995-08-31    | Ethiopia         |      2
  2 | Francis    | Nickerson     | fnickerson2@mac.com            | Male   | 1998-03-16    | Portugal         |      1
  3 | Erminie    | M'Quharg      | emquharg3@e-recht24.de         | Female | 1999-03-13    | Mozambique       |      7
  4 | Teodoro    | Trimmill      |                                | Male   | 1982-04-30    | China            |      5
  8 | Elset      | Agass         | eagass8@rambler.ru             | Female | 2004-06-26    | China            |      4
  7 | Letta      | Caurah        | lcaurah7@yale.edu              | Female | 1994-09-09    | Indonesia        |      6
  6 | West       | Elphey        |                                | Male   | 2004-03-29    | Indonesia        |      3
(10 rows)

```

### Delete Record with Foreign Keys


```
test=# DELETE FROM car WHERE id = 7;
ERROR:  update or delete on table "car" violates foreign key constraint "person_car_id_fkey" on table "person"
DETAIL:  Key (id)=(7) is still referenced from table "person".
test=# DELETE FROM person WHERE id = 3;
DELETE 1
test=# SELECT * FROM person;
 id | first_name |   last_name   |             email              | gender | date_of_birth | country_of_birth | car_id 
----+------------+---------------+--------------------------------+--------+---------------+------------------+--------
  5 | Reilly     | Amesbury      | ramesbury5@businessinsider.com | Male   | 1990-12-31    | China            |       
  9 | Aurore     | Drillingcourt | adrillingcourt9@cnet.com       | Female | 1977-10-19    | China            |       
 10 | Ilse       | Goldman       | igoldmana@ihg.com              | Female | 2001-07-31    | Mongolia         |       
  1 | Hamid      | Abbett        | habbett1@cbc.ca                | Male   | 1995-08-31    | Ethiopia         |      2
  2 | Francis    | Nickerson     | fnickerson2@mac.com            | Male   | 1998-03-16    | Portugal         |      1
  4 | Teodoro    | Trimmill      |                                | Male   | 1982-04-30    | China            |      5
  8 | Elset      | Agass         | eagass8@rambler.ru             | Female | 2004-06-26    | China            |      4
  7 | Letta      | Caurah        | lcaurah7@yale.edu              | Female | 1994-09-09    | Indonesia        |      6
  6 | West       | Elphey        |                                | Male   | 2004-03-29    | Indonesia        |      3
(9 rows)
```

It turns out that we can't delete a record which is assigned with the `person` table from the `car` table, but we can delete any record from the `person` table. This is because there is a relation from the `person` table to the `car` table.

To delete a record from the `car` table, we have to delete the corresponding record in the `person` table or set the `car_id` of that record to NULL.





## JOIN
A JOIN clause is used to combine rows from two or more tables, based on a related column between them.

### INNER JOIN

The INNER JOIN keyword selects records that have matching values in both tables.

The INNER JOIN creates a new result table by combining column values of two tables (table1 and table2) based upon the join-predicate. The query compares each row of table1 with each row of table2 to find all pairs of rows which satisfy the join-predicate. When the join-predicate is satisfied, column values for each matched pair of rows of A and B are combined into a result row.

```sql
SELECT column_name(s)
FROM table1
INNER JOIN table2
ON table1.column_name = table2.column_name;
```

![INNER JOIN](https://imgur.com/oQjyWQa.jpg, "INNER JOIN")


Now let's join our tables based on foreign keys.

<sub>_Command_</sub>
```sql
SELECT * FROM person
JOIN car ON person.car_id = car.id;
```

<sub>_Output_</sub>
```
 id | first_name | last_name |         email          | gender | date_of_birth | country_of_birth | car_id | id |    make    |      model       |   price   
----+------------+-----------+------------------------+--------+---------------+------------------+--------+----+------------+------------------+-----------
  2 | Francis    | Nickerson | fnickerson2@mac.com    | Male   | 1998-03-16    | Portugal         |      1 |  1 | Daewoo     | Leganza          | 241058.40
  1 | Hamid      | Abbett    | habbett1@cbc.ca        | Male   | 1995-08-31    | Ethiopia         |      2 |  2 | Mitsubishi | Montero          | 269595.21
  6 | West       | Elphey    |                        | Male   | 2004-03-29    | Indonesia        |      3 |  3 | Kia        | Rio              | 245275.16
  8 | Elset      | Agass     | eagass8@rambler.ru     | Female | 2004-06-26    | China            |      4 |  4 | Jaguar     | X-Type           |  41665.96
  4 | Teodoro    | Trimmill  |                        | Male   | 1982-04-30    | China            |      5 |  5 | Lincoln    | Mark VIII        | 163843.38
  7 | Letta      | Caurah    | lcaurah7@yale.edu      | Female | 1994-09-09    | Indonesia        |      6 |  6 | GMC        | Rally Wagon 3500 | 231169.05
  3 | Erminie    | M'Quharg  | emquharg3@e-recht24.de | Female | 1999-03-13    | Mozambique       |      7 |  7 | Cadillac   | Escalade ESV     | 279951.34
(7 rows)

```

<sub>_Command_</sub>
```sql
SELECT person.first_name, person.last_name, car.make, car.model, car.price
FROM person
JOIN car ON person.car_id = car.id;
```

<sub>_Output_</sub>
```
 first_name | last_name |    make    |      model       |   price   
------------+-----------+------------+------------------+-----------
 Francis    | Nickerson | Daewoo     | Leganza          | 241058.40
 Hamid      | Abbett    | Mitsubishi | Montero          | 269595.21
 West       | Elphey    | Kia        | Rio              | 245275.16
 Elset      | Agass     | Jaguar     | X-Type           |  41665.96
 Teodoro    | Trimmill  | Lincoln    | Mark VIII        | 163843.38
 Letta      | Caurah    | GMC        | Rally Wagon 3500 | 231169.05
 Erminie    | M'Quharg  | Cadillac   | Escalade ESV     | 279951.34
(7 rows)

```

### LEFT JOIN

The LEFT JOIN keyword returns all records from the left table (table1), and the matched records from the right table (table2). The result is NULL from the right side, if there is no match.

![LEFT JOIN](https://imgur.com/sS5mapo.jpg, "LEFT JOIN")

<sub>_Command_</sub>
```sql
SELECT person.first_name, person.last_name, car.make, car.model, car.price
FROM person
LEFT JOIN car ON person.car_id = car.id;

```

<sub>_Output_</sub>
```
 first_name |   last_name   |    make    |      model       |   price   
------------+---------------+------------+------------------+-----------
 Francis    | Nickerson     | Daewoo     | Leganza          | 241058.40
 Hamid      | Abbett        | Mitsubishi | Montero          | 269595.21
 West       | Elphey        | Kia        | Rio              | 245275.16
 Elset      | Agass         | Jaguar     | X-Type           |  41665.96
 Teodoro    | Trimmill      | Lincoln    | Mark VIII        | 163843.38
 Letta      | Caurah        | GMC        | Rally Wagon 3500 | 231169.05
 Erminie    | M'Quharg      | Cadillac   | Escalade ESV     | 279951.34
 Ilse       | Goldman       |            |                  |          
 Aurore     | Drillingcourt |            |                  |          
 Reilly     | Amesbury      |            |                  |          
(10 rows)

```


### RIGHT JOIN
The RIGHT JOIN keyword returns all records from the right table (table2), and the matched records from the left table (table1). The result is NULL from the left side, when there is no match.


![RIGHT JOIN](https://imgur.com/5ex2jIP.jpg, "RIGHT JOIN")



### FULL OUTER JOIN
The FULL OUTER JOIN keyword returns all records when there are a match in left (table1) or right (table2) table records.

Note: FULL OUTER JOIN can potentially return very large result-sets!

FULL OUTER JOIN and FULL JOIN are the same.

![FULL OUTER JOIN](https://imgur.com/AUaYHON.jpg, "FULL OUTER JOIN")


## Exporting Query Results to CSV


By typing `\?` and check the help. In the Input/Output section, it says that `\copy ...    perform SQL COPY with data stream to the client host`.


We will save this query to a CSV file.

```
test=# SELECT person.first_name, person.last_name, car.make, car.model, car.price
FROM person
LEFT JOIN car ON person.car_id = car.id;
 first_name |   last_name   |    make    |      model       |   price   
------------+---------------+------------+------------------+-----------
 Francis    | Nickerson     | Daewoo     | Leganza          | 241058.40
 Hamid      | Abbett        | Mitsubishi | Montero          | 269595.21
 West       | Elphey        | Kia        | Rio              | 245275.16
 Elset      | Agass         | Jaguar     | X-Type           |  41665.96
 Teodoro    | Trimmill      | Lincoln    | Mark VIII        | 163843.38
 Letta      | Caurah        | GMC        | Rally Wagon 3500 | 231169.05
 Ilse       | Goldman       |            |                  |          
 Aurore     | Drillingcourt |            |                  |          
 Reilly     | Amesbury      |            |                  |          
(9 rows)

```


<sub>_Command_</sub>
```sql
\copy (SELECT person.first_name, person.last_name, car.make, car.model, car.price FROM person LEFT JOIN car ON car.id = person.car_id) TO '/home/arafat_hasan/Downloads/results.csv' DELIMITER ',' CSV HEADER

```
<sub>_Output_</sub>
```
COPY 9

```
The query is stored in the CSV file.



## Serials and Sequences
```
test=# \d person;
                                         Table "public.person"
      Column      |          Type          | Collation | Nullable |              Default               
------------------+------------------------+-----------+----------+------------------------------------
 id               | bigint                 |           | not null | nextval('person_id_seq'::regclass)
 first_name       | character varying(50)  |           | not null | 
 last_name        | character varying(50)  |           | not null | 
 email            | character varying(150) |           |          | 
 gender           | character varying(7)   |           | not null | 
 date_of_birth    | date                   |           | not null | 
 country_of_birth | character varying(50)  |           | not null | 
 car_id           | bigint                 |           |          | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id)
    "person_car_id_key" UNIQUE CONSTRAINT, btree (car_id)
Foreign-key constraints:
    "person_car_id_fkey" FOREIGN KEY (car_id) REFERENCES car(id)

test=# SELECT * FROM person_id_seq ;
 last_value | log_cnt | is_called 
------------+---------+-----------
         10 |      23 | t
(1 row)

test=# SELECT nextval('person_id_seq'::regclass);
 nextval 
---------
      11
(1 row)

test=# SELECT nextval('person_id_seq'::regclass);
 nextval 
---------
      12
(1 row)

test=# SELECT * FROM person_id_seq ;
 last_value | log_cnt | is_called 
------------+---------+-----------
         12 |      32 | t
(1 row)

test=# ALTER SEQUENCE person_id_seq RESTART WITH 10;
ALTER SEQUENCE
test=# SELECT * FROM person_id_seq ;
 last_value | log_cnt | is_called 
------------+---------+-----------
         10 |       0 | f
(1 row)

```



## Extensions

Simply extensions are functions that can add extra functionality to the database.

List of available extensions

```
test=# SELECT * FROM pg_available_extensions;
  name   | default_version | installed_version |           comment            
---------+-----------------+-------------------+------------------------------
 plpgsql | 1.0             | 1.0               | PL/pgSQL procedural language
(1 row)

```

## UUID Datatype

From wikipedia:
    > A universally unique identifier (UUID) is a 128-bit number used to identify information in computer systems. The term globally unique identifier (GUID) is also used, typically in software created by Microsoft.

    > When generated according to the standard methods, UUIDs are, for practical purposes, unique. Their uniqueness does not depend on a central registration authority or coordination between the parties generating them, unlike most other numbering schemes. While the probability that a UUID will be duplicated is not zero, it is close enough to zero to be negligible. 



We have to add the uuid-ossp extension:
```
CREATE EXTENSION "uuid-ossp";
```

List of a available functions:

```
\df
```

Now we have to invoke the function:
```
SELECT uuid_generate_v4();
```

```
ANLONGUUID
```

### UUID as Primary Key

Drop `person` and `car` table and create another ones as below.


```sql
CREATE TABLE car (
	car_uid UUID NOT NULL PRIMARY KEY,
	make VARCHAR(100) NOT NULL,
	model VARCHAR(100) NOT NULL,
	price NUMERIC(19, 2) NOT NULL
);


CREATE TABLE person (
	person_uid UUID NOT NULL PRIMARY KEY,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(50) NOT NULL,
	email VARCHAR(150),
	gender VARCHAR(7) NOT NULL,
	date_of_birth DATE NOT NULL,
	country_of_birth VARCHAR(50) NOT NULL,
	car_uid UUID REFERENCES car(car_uid),
	UNIQUE(car_uid),
	UNIQUE(email)
);



INSERT INTO car (car_uid, make, model, price) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'Mitsubishi', 'Montero', '269595.21');

INSERT INTO car (car_uid, make, model, price) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'Kia', 'Rio', '245275.16');

INSERT INTO car (car_uid, make, model, price) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'Jaguar', 'X-Type', '41665.96');

INSERT INTO car (car_uid, make, model, price) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'Lincoln', 'Mark VIII', '163843.38');




INSERT INTO person (person_uid, first_name, last_name, email, gender, date_of_birth, country_of_birth) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'Hamid', 'Abbett', 'habbett1@cbc.ca', 'Male', '1995-08-31', 'Ethiopia');

INSERT INTO person (person_uid, first_name, last_name, email, gender, date_of_birth, country_of_birth) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'Francis', 'Nickerson', 'fnickerson2@mac.com', 'Male', '1998-03-16', 'Portugal');

INSERT INTO person (person_uid, first_name, last_name, email, gender, date_of_birth, country_of_birth) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'Erminie', 'M''Quharg', 'emquharg3@e-recht24.de', 'Female', '1999-03-13', 'Mozambique');

INSERT INTO person (person_uid, first_name, last_name, email, gender, date_of_birth, country_of_birth) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'Teodoro', 'Trimmill', null, 'Male', '1982-04-30', 'China');

INSERT INTO person (person_uid, first_name, last_name, email, gender, date_of_birth, country_of_birth) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'Reilly', 'Amesbury', 'ramesbury5@businessinsider.com', 'Male', '1990-12-31', 'China');

INSERT INTO person (person_uid, first_name, last_name, email, gender, date_of_birth, country_of_birth) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'West', 'Elphey', null, 'Male', '2004-03-29', 'Indonesia');

INSERT INTO person (person_uid, first_name, last_name, email, gender, date_of_birth, country_of_birth) 
VALUES (uuid_generate_v4(), uuid_generate_v4(), 'Letta', 'Caurah', 'lcaurah7@yale.edu', 'Female', '1994-09-09', 'Indonesia');

```

