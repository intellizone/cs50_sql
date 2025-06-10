# cs50_sql

> A concise reference and tutorial for SQL (with SQLite) and flat-file databases.  
> This document covers design principles, SQL basics, practical code examples, and tips for safe and effective database use.

---

**Table of Contents**
- [Design Principles](#design-principles)
- [Flat-file Database (CSV)](#flat-file-database-csv)
- [Relational Database (SQL with SQLite)](#relational-database-sql-with-sqlite)
  - [CRUD Operations](#crud-operations)
  - [Using `sqlite3`](#using-sqlite3)
  - [CREATE TABLE](#create-table)
  - [ALTER TABLE](#alter-table)
  - [INSERT INTO](#insert-into)
  - [SELECT Queries and Functions](#select-queries-and-functions)
  - [DELETE](#delete)
  - [UPDATE](#update)
- [Entity Relationship (ER) Diagrams](#entity-relationship-er-diagrams)
- [SQL Data Types & Constraints](#sql-data-types--constraints)
- [JOINS](#joins)
- [Indexes](#indexes)
- [Transactions & Race Conditions](#transactions--race-conditions)
- [SQL Injection Attacks](#sql-injection-attacks)
- [Best Practices](#best-practices)

---

## Design Principles
- Each table should represent a single entity
    - (e.g., `employee`, `employee_relationship`, `team`)
- Each piece of data should be stored in a single location
    - Use relationship tables and refer with unique IDs

## Flat-file Database (CSV)
```py
import csv

with open("filename.csv", "r") as file:
    reader = csv.reader(file)
    for row in reader:
        print(row[0])

# Reading with DictReader
import csv

with open("filename.csv", "r") as file:
    reader = csv.DictReader(file)
    for row in reader:
        print(row["language"])
```

## Relational Database (SQL with SQLite)

### CRUD Operations

- **C**reate (INSERT)
- **R**ead (SELECT)
- **U**pdate (UPDATE)
- **D**elete (DELETE, DROP)

### Using `sqlite3`
```sh
# Start SQLite CLI with a database file
sqlite3 favorites.db

.mode csv
.import favorites.csv favorites
.quit

.schema

# Download example data
wget https://raw.githubusercontent.com/MainakRepositor/Datasets/refs/heads/master/Pokemon.csv

.mode csv
.import Pokemon.csv pokemon
```

### CREATE TABLE
```sql
-- CREATE TABLE syntax
CREATE TABLE table_name (
    column1 datatype constraint,
    column2 datatype constraint,
    ...
);

-- Common constraints:
-- NOT NULL      Ensures column cannot be NULL
-- UNIQUE        Ensures all values in a column are unique
-- PRIMARY KEY   Unique identifier for rows (implies NOT NULL & UNIQUE)
-- FOREIGN KEY   Maintains referential integrity
-- CHECK         Ensures values satisfy a specific condition
-- DEFAULT       Sets a default value for a column
-- CREATE INDEX  Improves data retrieval speed

-- Example:
CREATE TABLE friend (
    id INTEGER NOT NULL UNIQUE,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    PRIMARY KEY(id)
);

-- PRIMARY KEY must be UNIQUE (no need to specify UNIQUE separately)
CREATE TABLE friend (
    id INTEGER NOT NULL,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    PRIMARY KEY(id)
);
```

### ALTER TABLE
```sql
-- ALTER TABLE table_name
-- ADD/DROP/RENAME/MODIFY column_name datatype;

ALTER TABLE friend MODIFY id UNIQUE;
```

### INSERT INTO
```sql
-- INSERT INTO table (column, ...) VALUES (value, ...);
INSERT INTO friend (id, first_name, last_name) VALUES (1, 'Sharmila', 'A');
INSERT INTO friend (first_name, last_name) VALUES ('Munusamy', 'M');
```

```sql
CREATE TABLE IF NOT EXISTS pokemon (
    "#" TEXT, "Name" TEXT, "Type 1" TEXT, "Type 2" TEXT,
    "Total" TEXT, "HP" TEXT, "Attack" TEXT, "Defense" TEXT,
    "Sp. Atk" TEXT, "Sp. Def" TEXT, "Speed" TEXT, "Generation" TEXT,
    "Legendary" TEXT
);
```

### SELECT Queries and Functions
```sql
SELECT * FROM pokemon;
.mode table
SELECT "Name" FROM pokemon;
SELECT COUNT(*) FROM pokemon; -- count of rows
SELECT DISTINCT "Type 1" FROM pokemon; -- unique elements
SELECT COUNT(DISTINCT "Type 2") FROM pokemon; -- count of distinct type 2

-- Functions:
-- AVG, COUNT, DISTINCT, LOWER, MAX, MIN, UPPER

-- GROUP BY, LIKE, LIMIT, ORDER BY, WHERE

-- How many Water type1 Pokémon?
SELECT COUNT(*) FROM pokemon WHERE "Type 1" = "Water";

-- How many Water type1 and Poison type2 Pokémon?
SELECT COUNT(*) FROM pokemon WHERE "Type 1" = "Water" AND "Type 2" = "Poison";

-- How many are actually Water type Pokémon (type1 or type2)?
SELECT COUNT(*) FROM pokemon WHERE "Type 1" = "Water" OR "Type 2" = "Water";

-- How many Pokémon are type1 Bug and type2 Grass or Poison?
SELECT COUNT(*) FROM pokemon WHERE "Type 1" = "Bug" AND ("Type 2" = "Grass" OR "Type 2" = "Poison");

-- LIKE keyword (similar to * in regex, use % in SQL)
SELECT COUNT(*) FROM pokemon WHERE language = 'C' AND problem LIKE 'Hello, %';

-- GROUP BY
SELECT "Type 1", COUNT(*) FROM pokemon GROUP BY "Type 1";

-- ORDER BY
SELECT "Type 1", COUNT(*) FROM pokemon GROUP BY "Type 1" ORDER BY COUNT(*);

-- ORDER BY DESC
SELECT "Type 1", COUNT(*) FROM pokemon GROUP BY "Type 1" ORDER BY COUNT(*) DESC;

-- Alias (rename) column
SELECT "Type 1", COUNT(*) as n FROM pokemon GROUP BY "Type 1" ORDER BY n DESC;

-- LIMIT (top 5)
SELECT "Type 1", COUNT(*) as n FROM pokemon GROUP BY "Type 1" ORDER BY n DESC LIMIT 5;
```

### DELETE
```sql
-- DELETE (be careful and mindful of what you are deleting!)
DELETE FROM pokemon WHERE "#" IS NULL;
```

### UPDATE
```sql
-- UPDATE table SET column = value WHERE condition;
UPDATE pokemon SET "Name" = 'Suriya' WHERE "Name" = 'Pikachu';
UPDATE pokemon SET "Name" = 'Pikachu' WHERE "Name" = 'Suriya';

-- Note: Use double quotes for keys, single quotes for values.
```

*NULL* → no value present

## Entity Relationship (ER) Diagrams
- One-to-one relationship
- One-to-many relationship
- Many-to-many relationship

*Tip: Use tools like [dbdiagram.io](https://dbdiagram.io/) or [draw.io](https://draw.io) to visualize ER diagrams.*

## SQL Data Types & Constraints

- Data Types: BLOB, INTEGER, NUMERIC, REAL, TEXT
- Constraints: NOT NULL, UNIQUE, PRIMARY KEY, FOREIGN KEY

## JOINS

- INNER JOIN
- OUTER JOIN

```sql
SELECT show_id FROM ratings WHERE rating >= 6.0 LIMIT 10;
SELECT * FROM shows WHERE id IN (SELECT show_id FROM ratings WHERE rating >= 6.0) LIMIT 10;
SELECT * FROM shows WHERE tconst IN (SELECT tconst FROM ratings WHERE averageRating >= 6.0) LIMIT 10;

SELECT * FROM show JOIN rating ON show.id = rating.show_id; -- duplicate id will also appear
SELECT primaryTitle, averageRating FROM shows JOIN ratings ON shows.tconst = ratings.tconst LIMIT 10;
SELECT primaryTitle, averageRating FROM shows JOIN ratings ON shows.tconst = ratings.tconst WHERE averageRating > 6.0 LIMIT 10;

-- Alternate join syntax:
SELECT primaryTitle, averageRating FROM shows, ratings WHERE shows.tconst = ratings.tconst LIMIT 10;
```

## Indexes

- SQLite uses B-tree indexes to speed up queries.

```sql
.timer ON

-- CREATE INDEX name ON table (column, ...);
CREATE INDEX title_index ON shows (primaryTitle);
```

## Transactions & Race Conditions

To avoid race conditions:
- Use transactions: `BEGIN TRANSACTION`, `COMMIT`, `ROLLBACK`

## SQL Injection Attacks

- Never trust user input.
- Always sanitize and use parameterized queries where possible.
- Assume the worst with user input.

## Best Practices

- Use parameterized queries to prevent SQL injection.
- Always back up your database before running destructive operations.
- Normalize data to reduce duplication.
- Use meaningful table and column names.
- Keep your schema documentation up to date.
- Use foreign keys to maintain referential integrity.
- Prefer explicit column lists in `SELECT` and `INSERT` for clarity.
- Use transactions for batch updates/inserts/deletes.

---

*Last updated: 2025-06-10*
