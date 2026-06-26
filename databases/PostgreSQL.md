# PostgreSQL Notes
Personal notes for creating and accessing PostgreSQL databases.

## Traditional Commands
- Data Definition Language (DDL)
- Data Query Language (DQL)
- Data Manipulation Language (DML)
- Data Control Language (DCL)

```
SQL Commands
|
|--DDL
|  |--CREATE
|  |--ALTER
|  |--DROP
|  |--RENAME
|  |--TRUNCATE
|  '--COMMENT
|--DQL
|  '--SELECT
|--DML
|  |--INSERT
|  |--UPDATE
|  |--DELETE
|  |--MERGE
|  |--CALL
|  |--EXPLAIN PLAN
|  '--LOCK TABLE
'--DCL
   |--GRANT
   '--REVOKE
```

## Login to a Database

Login to your database with the following command:

```
psql -U <user> <database>
```

NOTE: If no database name is given, postgres will assume connection to a database with the same name as the user. For example, in the following command, entering the user "postgres" will open the "postgres" database.

```
psql -U postgres
```

## Databases and Templates

- POSTGRES (Default Database)
- TEMPLATE0
- TEMPLATE1

TEMPLATE0 is used to create TEMPLATE1. Do not change TEMPLATE0.

TEMPLATE1 is used to create new databases.

NOTE: New databases cannot be created if TEMPLATE1 is being accessed. Have all users disconnect from TEMPLATE1 to create a new database.

### To Create a Template Database
```
mydatabase=# CREATE DATABASE supertemplate;

mydatabase=# CREATE TABLE supertable ();

mydatabase=# CREATE DATABASE superdatabase WITH TEMPLATE supertemplate;
-- this will create a new database with the supertemplate which includes the supertable
```

### To Create a Database
```
mydatabase=# CREATE DATABASE name
  [ [ WITH ] [ OWNER [=] user_name ]
    [ TEMPLATE [=] template ]
    [ ENCODING [=] encoding ]
    [ LC_COLLATE [=] lc_collate ]
    [ LC_CTYPE [=] lc_ctype ]
    [ TABLESPACE [=] tablespace ]
    [ CONNECTION LIMIT [=] connlimit ] ]
```
Default Settings:
- template1
- owner is current user
- encoding is UTF8
- connection limit is 100
- schema is "public"

Show all schemas:
```
mydatabase=# CREATE SCHEMA tech;
CREATE SCHEMA
mydatabase=# \dn
  List of schemas
  Name  |  Owner
--------+----------
 public | postgres
 tech   | postgres
(2 rows)
```

## Roles in Postgres

Roles have attributes and privileges

### Common Attributes
- LOGIN PRIVILEGE
- SUPERUSER STATUS
- DATABASE CREATION
- ROLE CREATION
- PASSWORD

```
mydatabase=# CREATE ROLE readonly WITH LOGIN ENCRYPTED PASSWORD 'readonly';
```

View Roles:
```
mydatabase=# \du
```
NOTE: by default, only creator of the database and superuser has access to the database objects

Create User:
```
mydatabase=# CREATE USER test_user WITH LOGIN ENCRYPTED PASSWORD 'password';
```

Note: You can also create a role within the CLI without being logged into postgres.
```
createuser --interactive
```
However, if you want to add a password to this role, you'll need to log into postgres and run:
```
mydatabase=# ALTER ROLE interactive_user_name WITH ENCRYPTED PASSWORD 'password';
```

Login method of postgres is set to 'Trust' by default. Modify the following files to enable password encryption.
- pg_hba.conf
- postgresql.conf

Find the path to these files by running the following commands:
For authentication connection methods...
```
mydatabase=# show hba_file;
/../pg_hba.conf
```
General configuration of PostgreSQL...
```
mydatabase=# show config_file;
/../postgresql.conf
```

### Granting Privileges

```
GRANT ALL PRIVILEGES ON <table> TO <user>;
GRANT ALL ON ALL TABLES [IN SCHEMA <schema>] TO <user>;
GRANT [SELECT, UPDATE, INSERT, ...] ON <table> [IN SCHEMA <schema>] TO <user>;
```