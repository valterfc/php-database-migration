PhpDbMigration - full PHP database migration tool
=================================================

This is a full standalone PHP tool based on [Symfony Console](http://symfony.com/doc/current/components/console)
and inspired by the Rails database migration tool and MyBatis. It merges the functionality of the two tools and
has been designed to be as flexible as possible.

Usage
-----
```
$ ./bin/migrate
Console Tool

Usage:
  command [options] [arguments]

Options:
  -h, --help            Display this help message
  -q, --quiet           Do not output any message
  -V, --version         Display this application version
      --ansi            Force ANSI output
      --no-ansi         Disable ANSI output
  -n, --no-interaction  Do not ask any interactive question
  -v|vv|vvv, --verbose  Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  help            Displays help for a command
  list            Lists commands
 migrate
  migrate:addenv  Initialise an environment to work with php db migrate
  migrate:create  Create a SQL migration
  migrate:down    Rollback all waiting migration down to [to] option if precised
  migrate:init    Create the changelog table on your environment database
  migrate:status  Display the current status of the specified environment
  migrate:up      Execute all waiting migration up to [to] option if precised
```

Installing it to your project
-----------------------------
Just add it to your composer.json (don't forget to specify your bin directory)
Warning, all migrate commands must be executed on your root folder like `bin/migrate migrate:command...`

    {
        "name": "jdoe/testproject",
        "authors": [
            {
                "name": "Jhon DOE",
                "email": "jdoe@gmail.com"
            }
        ],
        "require": {
            "valterfc/php-database-migration" :"3.7.*"
        },
        "config": {
            "bin-dir": "bin"
        }
    }


Adding an environment
---------------------
The first thing to do before playing with SQL migrations is to add an environment, let's add the dev one.

```
$ ./bin/migrate migrate:addenv
```

You will be prompted to answer a series of questions about your environment, and then a config file will be saved
in `./database/environments/[env].yml`.

Initialization
--------------
Once the environment is added, you have to initialize it. This verifies that the database connection works, and
creates a new database table for tracking the current database changes:

```
$ ./bin/migrate migrate:init [env]
```

Create a migration
------------------
It is time to create our first migration file.

```
$ ./bin/migrate migrate:create
```

Migrations file are like this

    -- // add table users
    -- Migration SQL that makes the change goes here.
    create table users (id integer, name text);
    -- @UNDO
    -- SQL to undo the change goes here.
    drop table users;

List migrations
------------------
View all available migrations and their status.

```
$ ./bin/migrate migrate:status [env]
+----------------+---------+------------------+--------------------+
| id             | version | applied at       | description        |
+----------------+---------+------------------+--------------------+
| 14679010838251 |         |                  | create table users |
+----------------+---------+------------------+--------------------+
```

Up and down
-----------
You can now up all the pending migrations. If you decide to down a migration, the last one will be downed alone to
prevent mistakes. You will be asked to confirm the downgrade of your database before running the real SQL script.

```
$ ./bin/migrate migrate:up [env]
```

You can mark migrations as applied without executing SQL (e.g. if you switched from another migration system)

```
$ ./bin/migrate migrate:up [env] --changelog-only
```

For development purposes, it is also possible to up a single migration without taking care of the other ones:

```
$ ./bin/migrate migrate:up [env] --only=[migrationid]
```

or migrate to specific migration (it will run all migrations, including the specified migration)

```
$ ./bin/migrate migrate:up [env] --to=[migrationid]
```

Same thing for down:

```
$ ./bin/migrate migrate:down [env] --only=[migrationid]
```

or


```
$ ./bin/migrate migrate:down [env] --to=[migrationid]
```


**Full Sample:**

```
cd your_project_directory

composer init

composer require valterfc/php-database-migration

./vendor/bin/migrate migrate:addenv

./vendor/bin/migrate migrate:init dev

./vendor/bin/migrate migrate:create dev

./vendor/bin/migrate migrate:status dev

./vendor/bin/migrate migrate:up dev

./vendor/bin/migrate migrate:down dev
```
### .env file

**For .env file (copy .env.example to your .env file):**
```
DB_HOST=127.0.0.1
DB_DRIVER=pgsql
DB_PORT=5432
DB_USER=postgres
DB_PWD=1234
DB_NAME=database_name
DB_CHARSET=utf8
```

**How to use .env:**

Create new env
```
./vendor/bin/migrate migrate:addenv
```

Answer yes to this question
```
Read database connection variables from System Variables? (yes/no) [yes]:
```

Answer yes to this question
```
Read database connection variables from System Variables? (yes/no) [yes]:
```

Select your .env file
```
DotEnv filename (.env)? (.envfilename/system - use System Variables) [.env]:
```

In the next questions, respond with the name of the variables used in the .env file.

Example:
```
Please enter your pdo driver: DB_DRIVER
```

Complete all fields
