# cs50_sql
## Design Principles
- Each table should be a collection of SINGLE entity
    - Like employee table, employee_relationship table, team table
- Each piece of data should be stored in SINGLE location
    - Relationship table
    - refer with unique id

## flat-file database
* csv
```py
import csv

with open("filename.csv", "r") as file:
    reader = csv.reader(file)
    for i in reader:
        print(i[0])


########################

import csv

with open("filename.csv", "r") as file:
    reader = csv.DictReader(file)
    for i in reader:
        print(i["language"])
```

## relational Database
* SQL

SQL: \
CRUD

C -> Create, Insert\
R -> Read --> Select \
U -> Update \
D -> Delete, Drop 

sqlite3 
```sh
# sqlite3 <filename>
sqlite3 favorites.db

.mode csv
.import favorites.csv favorites
.quit

.schema


wget https://raw.githubusercontent.com/MainakRepositor/Datasets/refs/heads/master/Pokemon.csv

.mode csv
.import Pokemon.csv pokemon
```
### CREATE table
```sql
-- CREATE TABLE table_name (
--     column1 datatype constraint,
--     column2 datatype constraint,
--     column3 datatype constraint,
--     ....
-- );
-- CONSTRAINT
-- NOT NULL - Ensures that a column cannot have a NULL value
-- UNIQUE - Ensures that all values in a column are different
-- PRIMARY KEY - A combination of a NOT NULL and UNIQUE. Uniquely identifies each row in a table
-- FOREIGN KEY - Prevents actions that would destroy links between tables
-- CHECK - Ensures that the values in a column satisfies a specific condition
-- DEFAULT - Sets a default value for a column if no value is specified
-- CREATE INDEX - Used to create and retrieve data from the database very quickly

CREATE TABLE friend("id" INTEGER NOT NULL UNIQUE, "first_name" TEXT NOT NULL, "last_name" TEXT NOT NULL, PRIMARY KEY("id"));

-- PRIMARY KEY must be UNIQUE so not needed to specify again
CREATE TABLE friend("id" INTEGER NOT NULL, "first_name" TEXT NOT NULL, "last_name" TEXT NOT NULL, PRIMARY KEY("id"));
```
### ALTER table
```sql
-- ALTER TABLE table_name
-- ADD/DROP/RENAME/ALTER/MODIFY column_name datatype;

ALTER TABLE friend MODIFY id UNIQUE;
```
### INSERT into table
```sql
-- INSERT INTO table (column, ...) VALUES(value, ...);
INSERT INTO friend("id", "first_name", "last_name") VALUES(1, 'sharmila', 'A');
INSERT INTO friend("first_name","last_name") VALUES('Munusamy', 'M' );
```
```sql
CREATE TABLE IF NOT EXISTS "pokemon"(
"#" TEXT, "Name" TEXT, "Type 1" TEXT, "Type 2" TEXT,
 "Total" TEXT, "HP" TEXT, "Attack" TEXT, "Defense" TEXT,
 "Sp. Atk" TEXT, "Sp. Def" TEXT, "Speed" TEXT, "Generation" TEXT,
 "Legendary" TEXT);

```

```sql
SELECT * from pokemon;
.mode table
SELECT "Name" from pokemon;
SELECT COUNT(*) from pokemon; -- count of rows
SELECT DISTINCT "Type 1" from pokemon; -- unique elements
SELECT count(DISTINCT "Type 2") from pokemon; -- count of distinct type 2(column)
-- some additional functions
-- AVG
-- COUNT
-- DISTINCT
-- LOWER
-- MAX 
-- MIN
-- UPPER

-- some more
-- GTOUP BY
-- LIKE
-- LIMIT
-- ORDER BY
-- WHERE

-- How many water type 1 pokemon are there
SELECT COUNT(*) FROM pokemon WHERE "Type 1" = "Water";
-- How many water type 1 nd poison type 2 pokemon are there
SELECT COUNT(*) FROM pokemon WHERE "Type 1" = "Water" AND "Type 2" = "Poison";

-- how many are actually water type pokemon like water can be present in any of type 1 or type 2
SELECT COUNT(*) FROM pokemon WHERE "Type 1" = "Water" OR "Type 2" = "Water";

-- how many pokemon are type 1 - water or grass and type 2 water
SELECT COUNT(*) FROM pokemon WHERE "Type 1" = "Bug" AND ("Type 2" = "Grass" OR "Type 2" = "Poison");
-- LIKE keyword - 
SELECT COUNT(*) FROM pokemon WHERE language = 'C' AND problem LIKE 'Hello, %'; -- in regex we use * here it is %

-- Group By
SELECT "Type 1", COUNT(*) FROM pokemon GROUP BY "Type 1";

-- ORDER BY <column name>
SELECT "Type 1", COUNT(*) FROM pokemon GROUP BY "Type 1" ORDER BY COUNT(*);

-- order by desc
SELECT "Type 1", COUNT(*) FROM pokemon GROUP BY "Type 1" ORDER BY COUNT(*) DESC;

-- alias(rename) column name
SELECT "Type 1", COUNT(*) as n FROM pokemon GROUP BY "Type 1" ORDER BY n DESC;

-- LIMIT top 5 data
SELECT "Type 1", COUNT(*) as n FROM pokemon GROUP BY "Type 1" ORDER BY n DESC LIMIT 5;

-- INSERT INTO
INSERT INTO table (column, ...) VALUES(value, ...);

INSERT INTO pokemon ("Name","Type 1","Type 2","Total","HP","Attack","Defense","Sp. Atk","Sp. Def","Speed","Generation","Legendary") VALUES('Dragon','Grass','Poison',318,45,49,49,65,65,45,1,'False');

-- DELETE  --------> be carful and mindful of what you are deleting
DELETE FROM pokemon WHERE "#" IS NULL;

-- UPDATE
UPDATE table SET column = value WHERE condition;
UPDATE pokemon SET "Name" = 'Suriya' WHERE "Name" = 'Pikachu'; -- be careful with single and double quote key should always be double quote and value should be single quote.
UPDATE pokemon SET "Name" = 'Pikachu' WHERE "Name" = 'Suriya';
```

* NULL -> no value there


## Entity Relationship(ER) diagram
- one to one relationship
- one to many relatioship
- many to many relationship 


## datatypes
- BLOB
- INTEGER
- NUMERIC
- REAL
- TEXT
---
- NOT NULL
- UNIQUE
---
- PRIMARY KEY
- FOREIGN KEY
---
- JOIN
    - INNER JOIN
    - OUTER JOIN
---
```sql
SELECT show_id FROM ratings WHERE rating >= 6.0 LIMIT 10;
SELECT * FROM shows WHERE id IN (SELECT show_id FROM ratings WHERE rating >= 6.0) LIMIT 10;
SELECT * FROM shows WHERE tconst in (SELECT "tconst" FROM ratings WHERE "averageRating" >= 6.0 ) LIMIT 10;
```

```sql
SELECT * FROM show JOIN rating ON show.id = rating.show_id; -- duplicate id will also populate
SELECT "primaryTitle","averageRating" FROM shows JOIN ratings ON shows.tconst = ratings.tconst LIMIT 10;
SELECT "primaryTitle","averageRating" FROM shows JOIN ratings ON shows.tconst = ratings.tconst WHERE "averageRating">6.0 LIMIT 10;

SELECT  "primaryTitle","averageRating" FROM shows, ratings WHERE shows.tconst = ratings.tconst LIMIT 10;  -- same join but with different approach
```

## index - B tree
```sql
.timer ON

-- CREATE INDEX name ON table (column, ...);
CREATE INDEX title_index ON shows (primaryTitle);
```

## race conditions
to avoid it we have some stuff

- BEGIN TRANSACTION
- COMMIT
- ROLLBACK
---

## SQL injection attacks
- never trust user input
- assume worst when it comes to user input

