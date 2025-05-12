# cs50_sql

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
