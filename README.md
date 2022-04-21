# SQL and Postgres Quick Reference

This repository is a quick personal reference to using Postgres and elaborating SQL queries.

- [SQL and Postgres Quick Reference](#sql-and-postgres-quick-reference)
  - [Installing Postgres with Docker](#installing-postgres-with-docker)
    - [Pull image](#pull-image)
    - [Start Postgres instance](#start-postgres-instance)
    - [Bash into instance](#bash-into-instance)
    - [Login with root superuser access](#login-with-root-superuser-access)
    - [Crate a new database](#crate-a-new-database)
    - [Alternative: Connect <em>psql</em> to docker container (needs <em>pqsl/postgres</em> installed locally)](#alternative-connect-psql-to-docker-container-needs-pqslpostgres-installed-locally)
  - [Postgres Cheat Sheet](#postgres-cheat-sheet)
  - [Hands-on SQL](#hands-on-sql)
    - [Creating a database](#creating-a-database)
    - [Connecting to databases](#connecting-to-databases)
    - [Create a table without constraints](#create-a-table-without-constraints)
    - [Drop table](#drop-table)
    - [Create table with constraints](#create-table-with-constraints)
    - [Insert data](#insert-data)
    - [Selects](#selects)
    - [Adding data](#adding-data)
    - [Sorting](#sorting)
    - [Remove duplicates](#remove-duplicates)
    - [Where clause](#where-clause)
    - [Limit, Offset and Fetch](#limit-offset-and-fetch)
    - [In](#in)
    - [Between](#between)
    - [Like](#like)
    - [Group by](#group-by)
    - [Having](#having)
    - [Union](#union)
    - [Union all](#union-all)
    - [Except and Intersect](#except-and-intersect)
  - [Aggregate functions](#aggregate-functions)
    - [Max, Min, Avg](#max-min-avg)
    - [Sum](#sum)
  - [Arithmetic Operators](#arithmetic-operators)
    - [Working with Arithmetic Operators](#working-with-arithmetic-operators)
  - [Alias](#alias)
  - [Null handling](#null-handling)
    - [Coalesce](#coalesce)
    - [Null if](#null-if)
  - [Dates](#dates)
    - [Adding &amp; Subtracting Dates](#adding--subtracting-dates)
    - [Extracting Fields From Dates](#extracting-fields-from-dates)
    - [tableAge function](#tableage-function)
  - [DDL - Data Definition Language](#ddl---data-definition-language)
    - [Create, Alter and Drop](#create-alter-and-drop)
    - [Primary Key Constraint](#primary-key-constraint)
    - [Unique Constraint](#unique-constraint)
    - [Check Constraint](#check-constraint)
  - [DML - Data Manipulation Language](#dml---data-manipulation-language)
    - [Selecting records](#selecting-records)
    - [Inserting Records](#inserting-records)
    - [Deleting Records](#deleting-records)
    - [Updating Records](#updating-records)
    - [On Conflict Do Nothing](#on-conflict-do-nothing)
    - [On Conflict Do Update](#on-conflict-do-update)
  - [Relationships (Foreign Keys &amp; Joins)](#relationships-foreign-keys--joins)
    - [Updating Foreign Keys](#updating-foreign-keys)
    - [Inner Joins](#inner-joins)
    - [Left Joins](#left-joins)
    - [Deleting Foreign Keys](#deleting-foreign-keys)
  - [Sequences](#sequences)
  - [Extensions](#extensions)
    - [Using UUIDs](#using-uuids)
  - [Exporting to CSV](#exporting-to-csv)
  - [References](#references)

## Installing Postgres with Docker

### Pull image

` $ docker pull postgres:alpine`

### Start Postgres instance

`$ docker run --name postgres-0 -e POSTGRES_PASSWORD=password -d -p 5432:5432 postgres:alpine`

### Bash into instance

` $ docker exec -it postgres-0 bash`

### Login with root superuser access

` $ psql -U postgres`

### Crate a new database

`$ CREATE DATABASE test; `

### Alternative: Connect _psql_ to docker container (needs _pqsl/postgres_ installed locally)

`$ psql -h localhost -p 5432 -U postgres`

## Postgres Cheat Sheet

| Action                                       | PostgreSQL command    |
| -------------------------------------------- | --------------------- |
| Help - list commands                         | `\?`                  |
| Access the PostgreSQL server from _psql_     | `psql -U [username];` |
| Connect to a specific database               | `\c database_name;`   |
| Quit the _psql_                              | ` \q`                 |
| List all databases                           | `\l`                  |
| Describe database                            | `\d`                  |
| Describe table                               | `\d table_name`       |
| List/describe all tables in current database | `\dt`                 |
| Execute file                                 | `\i file_path`        |
| Extended display on (transpose)              | `\x`                  |
| Describe available functions                 | `\df`                 |

[Link to complete Postgres Cheat Sheet](https://postgrescheatsheet.com/#/tables)

## Hands-on SQL

### Creating a database

`$ CREATE DATABASE test;`

### Connecting to databases

`$ psql -h localhost -p 5432 -U postgres test`

### Create a table without constraints

```sql
CREATE TABLE person (
    id INT,
    fisrt_name VARCHAR(50),
    lastname VARCHAR(50),
    gender VARCHAR(7),
    date_of_birth DATE
);
```

### Drop table

```sql
DROP TABLE person;
```

### Create table with constraints

```sql
CREATE TABLE person (
    id BIGSERIAL NOT NULL PRIMARY KEY,
    fisrt_name VARCHAR(50) NOT NULL,
    lastname VARCHAR(50) NOT NULL,
    gender VARCHAR(7) NOT NULL,
    date_of_birth DATE NOT NULL,
    email VARCHAR(50)
);
```

### Insert data

```sql
INSERT INTO person (fisrt_name, lastname, gender, date_of_birth, email)
	VALUES ('André', 'Kunitz', 'MALE', DATE '1990-04-04', 'andrekunitz@gmail.com');
```

### Selects

```sql
SELECT * FROM person;
SELECT fisrt_name, lastname FROM person;
```

### Adding data

At this stage, we will create fake date to interact with the database e create several queries

- To create mocked date, we can use Mockaroo: [Create mock data with Mockaroo](https://www.mockaroo.com/)

- For the purpose of this training, we can import data for the _person_ table: [person.sql file](/person.sql)

- To execute a SQL script file in Postgres, we can use the `\i` command:

  ​ `$ \i ~/Desktop/person.sql`

### Sorting

```sql
SELECT * FROM person ORDER BY country_of_birth;
SELECT * FROM person ORDER BY country_of_birth ASC;
SELECT * FROM person ORDER BY id DESC;
SELECT * FROM person ORDER BY first_name;
SELECT * FROM person ORDER BY id, email;
```

### Remove duplicates

```sql
SELECT DISTINCT country_of_birth FROM person ORDER BY country_of_birth DESC;
```

### Where clause

```sql
SELECT * FROM person WHERE gender = 'Male';
SELECT * FROM person WHERE gender = 'Male' AND country_of_birth = 'Brazil';
SELECT * FROM person WHERE gender = 'Male' AND (country_of_birth = 'Brazil' OR country_of_birth = 'Canada');
```

### Limit, Offset and Fetch

```sql
-- Limits to 10 results (LIMIT nowadays is widely support throughout databases)
SELECT * FROM person LIMIT 10;

-- Discard first 5 results and limits to 5 results (6, 7, 8, 9, 10)
SELECT * FROM person OFFSET 5 LIMIT 5;

-- Official limit method with FETCH (SQL Standard)
SELECT * FROM person OFFSET 5 FETCH FIRST 5 ROW ONLY;
SELECT * FROM person OFFSET 5 FETCH FIRST 1 ROW ONLY;
SELECT * FROM person OFFSET 5 FETCH FIRST ROW ONLY;
```

### In

Array of values and returns results matching these values

```sql
SELECT * FROM person
	WHERE country_of_birth IN ('Brazil', 'China', 'France', 'Mexico')
	ORDER BY country_of_birth;
```

### Between

Selects data from a range

```sql
SELECT * FROM person
	WHERE date_of_birth BETWEEN DATE '2000-01-01' AND '2022-01-01';
```

### Like

An underscore (`_`) in _`pattern`_ stands for (matches) any single character; a percent sign (`%`) matches any sequence of zero or more characters.

```sql
SELECT * FROM person WHERE email LIKE '%@google.%';

-- Use underscore to match single characters (8 chars followed by @)
SELECT * FROM person WHERE email LIKE '_______@';

-- ILIKE ignores uppercase
SELECT * FROM person WHERE country_of_birth ILIKE 'p%';
```

### Group by

```sql
SELECT country_of_birth, COUNT(*) FROM person
	GROUP BY country_of_birth
	ORDER BY country_of_birth;
```

### Having

Specifies a search condition for a group.

```sql
SELECT country_of_birth, COUNT(*) FROM person
	GROUP BY country_of_birth
	HAVING COUNT(*) > 5
	ORDER BY country_of_birth;
```

### Union

The `UNION` operator is used to combine the result-set of two or more `SELECT` statements.

- Every `SELECT` statement within `UNION` must have the same number of columns
- The columns must also have similar data types
- The columns in every `SELECT` statement must also be in the same order

```sql
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

### Union all

The `UNION` operator selects only distinct values by default (aka.: `SELECT DISTINCT`). To allow duplicate values, use `UNION ALL`:

```sql
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;
```

### Except and Intersect

## Aggregate functions

[Link to aggregate functions](https://www.postgresql.org/docs/9.5/functions-aggregate.html)

### Max, Min, Avg

```sql
SELECT MAX(price) FROM car;
SELECT MIN(price) FROM car;
SELECT AVG(price) FROM car;
SELECT ROUND(AVG(price)) FROM car;
SELECT make, MIN(price) FROM car GROUP BY make;
```

### Sum

```sql
SELECT SUM(price) FROM car;
SELECT make, SUM(price) FROM car GROUP BY make;
```

## Arithmetic Operators

```sql
SELECT 10 + 2;
SELECT 5!;
SELECT 10²;
SELECT 10 % 4;
```

### Working with Arithmetic Operators

```sql
SELECT make, model, price, ROUND(price * .10, 2),  ROUND((price - price * .10), 2) FROM car;
```

## Alias

```sql
SELECT
	make,
	model,
	price AS original_price,
    ROUND(price * .10, 2) AS ten_percent,
    ROUND((price - price * .10), 2) AS discount_after_ten_percent
FROM car;
```

## Null handling

### Coalesce

Coalesce to the first value which is present.

```sql
-- returns 1
SELECT COALESCE(null, null, 1, 10) AS number;

SELECT COALESCE(email, 'Email not provided') FROM person;
```

### Null if

Takes two arguments and returns the first argument if the second is not equal to the first.

```sql
-- returns null
SELECT NULLIF(10, 10);

-- returns 10
SELECT NULLIF(10, 20);

-- Handling division by 0
SELECT COALESCE(10 / NULLIF(0, 0), 0);
```

## Dates

[Date and Time types](https://www.postgresql.org/docs/current/datatype-datetime.html)

```sql
SELECT NOW();
SELECT NOW()::DATE;
SELECT NOW()::TIME;
```

### Adding & Subtracting Dates

```sql
SELECT NOW() - INTERVAL '1 YEAR';
SELECT NOW() - INTERVAL '10 MONTHS';
SELECT NOW()::DATE + INTERVAL '10 DAYS';
SELECT (NOW() + INTERVAL '10 DAYS')::DATE;
```

### Extracting Fields From Dates

```sql
SELECT EXTRACT(YAER FROM NOW());
SELECT EXTRACT(DOW FROM NOW());
SELECT EXTRACT(CENTURY FROM NOW());
```

### tableAge function

```sql
SELECT first_name, last_name, date_of_birth, AGE(NOW(), date_of_birth) AS age FROM person;
-- then extract fields from dates
```

## DDL - Data Definition Language

### Create, Alter and Drop

```sql
CREATE TABLE customers(
   customer_id INT GENERATED ALWAYS AS IDENTITY,
   customer_name VARCHAR(255) NOT NULL,
   PRIMARY KEY(customer_id)
);

CREATE TABLE contacts(
   contact_id INT GENERATED ALWAYS AS IDENTITY,
   customer_id INT,
   contact_name VARCHAR(255) NOT NULL,
   phone VARCHAR(15),
   email VARCHAR(100),
   PRIMARY KEY(contact_id),
   CONSTRAINT fk_customer
      FOREIGN KEY(customer_id)
	  REFERENCES customers(customer_id)
);
```

```sql
DROP DATABASE database_name;
DROP TABLE table-name;
```

```sql
-- Add column to table
ALTER TABLE table_name ADD column_name datatype;

-- Remove column from table
ALTER TABLE table_name DROP COLUMN column_name;

-- Change column data type
ALTER TABLE table_name ALTER COLUMN column_name datatype;
```

### Primary Key Constraint

```sql
ALTER TABLE person DROP CONSTRAINT person_pkey;
ALTER TABLE person ADD CONSTRAINT PRIMARY KEY (id);
```

### Unique Constraint

```sql
-- Gives unique constraint name
ALTER TABLE person ADD CONSTRAINT unique_email_address UNIQUE (email);

ALTER TABLE person DROP CONSTRAINT unique_email_address;

-- Database generates constraint name
ALTER TABLE person ADD UNIQUE (email);
```

### Check Constraint

Check constraint allow us only to add a string that matches the argument.

```sql
ALTER TABLE person ADD CONSTRAINT gender_constraint CHECK (gender == 'Female' OR 'Male');
```

## DML - Data Manipulation Language

### Selecting records

```sql
SELECT column1, column2, ... FROM table_name;
```

### Inserting Records

```sql
INSERT INTO person (first_name, last_name, gender, email, date_of_birth, country_of_birth)
	VALUES ('John', 'Doe', 'Male', 'johnd@gmail.com', DATE '1960-01-01', 'Canada');

-- Multirow insert
INSERT INTO table_name (column_list)
VALUES
    (value_list_1),
    (value_list_2),
    ...
    (value_list_n);
```

### Deleting Records

```sql
-- BE CAREFUL: Deletes every record in the table
DELETE FROM person;

-- Selectively deletes record
DELETE FROM person WHERE id = 1;
```

### Updating Records

```sql
-- BE CAREFUL: Update every entry in email column
UPDATE person SET email = 'test@gmail.com';

-- Selectively deletes record
UPDATE person SET email = 'test@gmail.com' WHERE id = 1;

-- Update multiple columns
UPDATE person SET email = 'test@gmail.com', name = 'John Doe' WHERE id = 1;
```

### On Conflict Do Nothing

Handle errors. To use it, column must be a primary key or have a unique constraint.

```sql
INSERT INTO person (id, first_name, last_name, gender, email, date_of_birth, country_of_birth)
	VALUES (1, 'John', 'Doe', 'Male', 'johnd@gmail.com', DATE '1960-01-01', 'Canada')
	ON CONFLICT (id) DO NOTHING;

INSERT INTO person (id, first_name, last_name, gender, email, date_of_birth, country_of_birth)
	VALUES (1, 'John', 'Doe', 'Male', 'johnd@gmail.com', DATE '1960-01-01', 'Canada')
	ON CONFLICT (email) DO NOTHING;
```

### On Conflict Do Update

In this case, we want to update certain fields if the insert encounters an error.

```sql
-- email refers to the current email and EXCLUDED.email refers to the one inste de VALUES array
INSERT INTO person (id, first_name, last_name, gender, email, date_of_birth, country_of_birth)
	VALUES (1, 'John', 'Doe', 'Male', 'johnd@gmail.com.br', DATE '1960-01-01', 'Canada')
	ON CONFLICT (id) DO UPDATE SET email = EXCLUDED.email;

INSERT INTO person (id, first_name, last_name, gender, email, date_of_birth, country_of_birth)
	VALUES (1, 'John', 'Doe', 'Male', 'johnd@gmail.com.br', DATE '1960-01-01', 'Canada')
	ON CONFLICT (id) DO UPDATE SET email = EXCLUDED.email, first_name = EXCLUDED.first_name;
```

## Relationships (Foreign Keys & Joins)

To follow along, remove the current data and recreate tables and date using the given script.

```sql
DROP TABLE person;
DROP TABLE car;
```

`$ \i ~/Desktop/person-car.sql`

### Updating Foreign Keys

```sql
UPDATE person SET car_id = 2 WHERE id = 1;
UPDATE person SET car_id = 1 WHERE id = 2;

-- Won't work. A car can only be owned by one person due to unique constraint.
UPDATE person SET car_id = 2 WHERE id = 2;
```

### Inner Joins

Links two tables where primary key and the foreign key is found in both tables.

```sql
SELECT * FROM person
JOIN car ON person.car_id = car.id;

SELECT person.first_name, car.make, car.model FROM person
JOIN car ON person.car_id = car.id;
```

### Left Joins

Links two tables including records that don't have a foreign key relationship (aka.: also includes people that don't have a car).

```sql
SELECT * FROM person
LEFT JOIN car ON person.car_id = car.id;

SELECT * FROM person
LEFT JOIN car ON person.car_id = car.id
WHERE car.* IS NULL;
```

### Deleting Foreign Keys

```sql
DELETE FROM person WHERE id = 1;
DELETE FROM car WHERE id = 2;
```

## Sequences

```sql
SELECT * FROM person_id_seq;
SELECT nextval('person_id_seq'::regclass);
SELECT * FROM person_id_seq;

-- restart sequence
ALTER SEQUENCE person_id_seq RESTART WITH 10;
SELECT * FROM person_id_seq;
```

## Extensions

```sql
SELECT * FROM pg_available_extensions ORDER BY name;

CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

SELECT uuid_generate_v4();
```

### Using UUIDs

Example: [person-car-uuid.sql](./person-car-uuid.sql)

```sql
SELECT * FROM person LEFT JOIN car ON person.car_uid = car.car_uid;

-- Since the column has the same name in both tables, we can use:
SELECT * FROM person LEFT JOIN car USING (car_uid);

SELECT * FROM person LEFT JOIN car USING (car_uid) WHERE car.* IS NULL;
```

## Exporting to CSV

```sql
\copy (SELECT * FROM person LEFT JOIN car ON person.car_uid = car.car_uid;) TO '~/Desktop/results.csv' DELIMITER ',' CSV HEADER;
```

## References

The content of this repository was based on [this](https://www.youtube.com/watch?v=5hzZtqCNQKk) course and some personal research.
