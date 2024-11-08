# Postgres

<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

* [Command line](#command-line)
  - [Connect as a Postgres user](#connect-as-a-postgres-user)
  - [Login as Postgres user and launch the psql tool](#login-as-postgres-user-and-launch-the-psql-tool)
  - [Connect to a different database if necessary](#connect-to-a-different-database-if-necessary)
  - [You can use SQL queries now (don't forget trailing semicolon!)](#you-can-use-sql-queries-now-dont-forget-trailing-semicolon)
* [psql](#psql)
* [psql commands (meta-commands)](#psql-commands-meta-commands)
* [Grants](#grants)
* [Change user password](#change-user-password)
* [Back-up](#back-up)
    + [Manual (one-shot)](#manual-one-shot)
    + [Automate back-up with a cron task](#automate-back-up-with-a-cron-task)
* [On Azure](#on-azure)

<!-- TOC end -->

## Command line

#### Connect as a postgres user

Switch current user to the postgres user at OS system (Linux/Unix) => manage system level tasks or managing Postgres with admin privileges

`su -U postgres`

Start the Postgres interactive terminal as the postgres user => interact with Postgres databases, run SQL queries, manage database objects

`psql -U postgres`

---

#### Login as Postgres user and launch the psql tool

`psql -U <USER> -d <DATABASE> -h <HOST> -p 5432`

Host can be localhost (on local machine for example). If already logged, just typing `psql` will connect to the default database. 

#### Connect to a different database if necessary

`\c <DBNAME>`

#### You can use SQL queries now (don't forget trailing semicolon!)

## psql

Pass a SQL command: **-c / --command**

`psql -U <USER> -c "drop database <DBNAME>`

## psql commands (meta-commands)

| Command                         | What is does                                                                                       |
|---------------------------------|----------------------------------------------------------------------------------------------------|
| `\c <DBNAME>`                   | Connect to a database.                                                                             |
| `\d (<SCHEMANAME>.)<TABLENAME>` | Describe a table, showing its columns and their types.                                             |
| `\dt`                           | List all tables in the current database. => default schema "public"                                |
| `\dt <SCHEMANAME>.*`            | List all tables in the current database in <SCHEMANAME>                                            |
| `\dt *.*`                       | List all tables in the current database in all schemas                                             |
| `\dn`                           | List all tables all schemas in the current database                                                |
| `\dn+`                          | List all tables all schemas with additional details such as **privileges**, owner and descriptions |
| `\du`                           | List all users.                                                                                    |
| `\l`                            | List all databases with granted users.                                                             |
| `\q`                            | Quit the psql terminal.                                                                            |
| `\i <FILENAME>`                 | Execute SQL commands from a file (usually .txt or .sql file).                                      |
| `\h <COMMAND>`                  | Get help on a specific SQL command.                                                                |
| `\?`                            | List all psql commands.                                                                            |

## Grants

Note: Check ownership and permissions at database AND schema level

`CREATE USER <USER> WITH ENCRYPTED PASSWORD <PWD>`

`GRANT CONNECT ON DATABASE <DBNAME> TO <USER>;`

Allow access to objects without create/alter rights

`GRANT USAGE ON SCHEMA <SCHEMANAME> TO <USER>;`

`GRANT ALL PRIVILEGES ON DATABASE <DBNAME> TO <USER>;`

/!\ Privileges are applied on existing tables

`GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA <SCHEMANAME> TO <USER>;`

`GRANT ALL PRIVILEGES ON <TABLENAME> IN SCHEMA <SCHEMANAME> TO <USER>;`

`GRANT <SELECT/INSERT/UPDATE/DELETE> ON <TABLENAME> IN SCHEMA <SCHEMANAME> TO <USER>;`

`GRANT <SELECT, INSERT, UPDATE> ON <TABLENAME> IN SCHEMA <SCHEMANAME> TO <USER>;`

`GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA <SCHEMANAME> TO <USER>;`

Allow the user to grant the same privileges to others

`GRANT SELECT ON <TABLENAME> IN SCHEMA <SCHEMANAME> TO <USER> WITH GRANT OPTION;`

Set default privileges for a user for newly created tables

`ALTER DEFAULT PRIVILEGES IN SCHEMA <SCHEMANAME> GRANT SELECT ON TABLES TO <USER>`

Give a user permission to create a database

`ALTER USER <USER> CREATEDB`

Make a user superuser

`ALTER USER <USER> WITH SUPERUSER`

Remove superuser status of a user

`ALTER USER <USER> WITH NOSUPERUSER`

## Change user password

In the psql interactive terminal (will prompt to enter the new password)

`\password <USER>`

From the psql prompt -> useful for scripting/automating

`ALTER USER <USER> WITH PASSWORD <NEW_PWD>;`

## Back-up

Switch to postgres default admin user

`su - postgres`

### Manual (one-shot)

Create a manual backup of a database. Output file can be in bak, sql or tar format.

`pg_dump <DBNAME> > <BACKUPNAME>.bak`

Restore from dump (different for a plain sql script or an archive)

`psql <DBNAME> < <BACKUPNAME>.sql`

`pg_restore -d <DBNAME> -f <BACKUPNAME>.bak`

Dump all databases

`pg_dumpall > <BACKUPNAME>.sql`

Restore all:

`psql -f <BACKUPNAME>.sql <DBNAME>`

### Automate back-up with a cron task

Create a folder for backups (-p = create folder hierarchy as needed)

`mkdir -p ~/postgres/<FOLDERNAME>`

Edit the cron table for current user (=> must be connected with postgres admin user)

`crontab -e`

Add a line with the desired cron expression. Example: at midnight every Sunday, execute the command `pg_dump -U postgres <DBNAME> > ~/postgres/<FOLDERNAME>/<BACKUPNAME>.bak` in the environment of the postgres user. The `pg_dump`command is implied thanks to the format of the expression.

`0  0  *  * 0  -U   postgres   <DBNAME>   >   ~/postgres/<FOLDERNAME>/<BACKUPNAME>.bak`

## On Azure

Access from terminal:

`psql "host=<HOST>.postgres.database.azure.com port=5432 dbname=<DBNAME> user=<USER> password=<PWD> sslmode=require"` 

Connection string:

`jdbc:postgresql://<HOST>.postgres.database.azure.com:5432/<DBNAME>?user=<USER>&password=<PWD>&sslmode=require `