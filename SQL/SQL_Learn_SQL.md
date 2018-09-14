# Learn SQL

## Manipulation

* SQL: Structured Query Language
* RDBMS: Relational Database Management System

### 基本語法
* **CREATE TABLE** creates a new table.
  
```SQL
CREATE TABLE table_name (
  column_1 data_type, 
  column_2 data_type, 
  column_3 data_type
);
```

```SQL
CREATE TABLE celebs (
  id INTEGER,
  name TEXT,
  age INTEGER
);
```

* **INSERT INTO** adds a new row to a table.
  
```SQL
INSERT INTO table_name (column_1, column_2, column_3)
VALUES (value_1, value_2, value_3);
```

```SQL
INSERT INTO celebs (id, name, age)
VALUES (1, 'Justin Bieber', 21);
SELECT * FROM celebs;
```

```SQL
INSERT INTO celebs (id, name, age) 
VALUES (2, 'Beyonce Knowles', 33); 

INSERT INTO celebs (id, name, age) 
VALUES (3, 'Jeremy Lin', 26); 

INSERT INTO celebs (id, name, age) 
VALUES (4, 'Taylor Swift', 26);

SELECT name FROM celebs;
```

* **SELECT** queries data from a table.

選擇全部欄位: 使用 *

```SQL
SELECT * FROM table_name;
```

選擇特定欄位: 列出欄位名稱

```SQL
SELECT column_1, column_2, column_3 FROM table_name;
```

* **UPDATE** edits a row in a table.

```SQL
UPDATE celebs
SET age = 22
WHERE id = 1;

SELECT * FROM celebs;
```

* **ALTER TABLE** changes an existing table.
  
```SQL
ALTER TABLE celebs ADD COLUMN
twitter_handle TEXT;

SELECT * FROM celebs;
```

* **DELETE FROM** deletes rows from a table.
  
```SQL
UPDATE celebs
SET twitter_handle = '@taylorswift13'
WHERE id = 4;

DELETE FROM celebs
WHERE twitter_handle IS NULL;
SELECT * FROM celebs;
```
在 SQL 中，空值是 `NULL`，可以用 `IS NULL` 和 `IS NOT NULL` 來判斷是否是空值

* Constrains

```SQL
CREATE TABLE awards (
  id INTEGER PRIMARY KEY,
  recipient TEXT NOT NULL,
  award_name TEXT DEFAULT "Grammy");
```

```SQL
CREATE TABLE celebs (
   id INTEGER PRIMARY KEY, 
   name TEXT UNIQUE,
   date_of_birth TEXT NOT NULL,
   date_of_death TEXT DEFAULT 'Not Applicable',
);
```

## Queries

* **SELECT** is the clause we use every time we want to query information from a database.

```SQL
SELECT column1, column2 
FROM table_name;
```

```SQL
SELECT * FROM movies;
```

```SQL
SELECT name, genre
FROM movies;

select name, genre, year
FROM movies;
```

* **AS** renames a column or table.

```SQL
SELECT name AS 'Titles'
FROM movies;
```

```SQL
SELECT name AS 'Binge'
FROM movies;

SELECT imdb_rating AS 'IMDb' 
FROM movies;
```

* **DISTINCT** return unique values.

```SQL
SELECT tools 
FROM inventory;

SELECT DISTINCT tools 
FROM inventory;
```

```SQL
SELECT DISTINCT genre
FROM movies;

SELECT DISTINCT year
FROM movies;
```

* **WHERE** is a popular command that lets you filter the results of the query based on conditions that you specify.

```SQL
SELECT *
FROM movies
WHERE imdb_rating > 8;

SELECT *
FROM movies
WHERE imdb_rating < 5;

SELECT * 
FROM movies 
WHERE year > 2014;
```

* **LIKE** and **BETWEEN** are special operators.
  * 使用通配符 `_` (單一字元) 和 `%` (多字元)

```SQL
SELECT * 
FROM movies
WHERE name LIKE 'Se_en';
```
`Se_en`: 找所有 Se 和 en 中間有一個字元的
    
```SQL
SELECT * 
FROM movies
WHERE name LIKE 'A%';

SELECT * 
FROM movies 
WHERE name LIKE '%man%';
```
`A%`: 找所有 A 開頭的，
`%man%`: 找所有字串中有 man 的
    
```SQL
SELECT *
FROM movies
WHERE name LIKE '%man';

SELECT *
FROM movies
WHERE name LIKE 'The %';
```
`%man`: 找所有 man 結尾的，
`The %`: 找所有 The 開頭的

* **BETWEEN** 是放在 **WHERE** 子句中使用
  * 字母的話不包含 `AND` 後者，數字的話包含 `AND` 後者
    
```SQL
SELECT *
FROM movies
WHERE name BETWEEN 'A' AND 'J';

SELECT *
FROM movies
WHERE year BETWEEN 1990 AND 1999;
```
不包含 `J`，包含 `1999`
    
```SQL
SELECT *
FROM movies
WHERE name BETWEEN 'D' AND 'G';

SELECT *
FROM movies
WHERE year BETWEEN 1970 AND 1979;
```
不包含 `G` 包含 `1979`
    
* **IS NULL** 和 **IS NOT NULL**

```SQL
SELECT name
FROM movies 
WHERE imdb_rating IS NOT NULL;

SELECT name
FROM movies
WHERE imdb_rating IS NULL;
```

* **AND** and **OR** combines multiple conditions.
  * **AND**
  
```SQL
SELECT * 
FROM movies
WHERE year BETWEEN 1990 AND 1999
   AND genre = 'romance';
```
    
```SQL
SELECT *
FROM movies
WHERE year BETWEEN 1970 AND 1979
   AND imdb_rating > 8;
   
SELECT *
FROM movies
WHERE year < 1985
   AND genre="horror";
```
    
  * **OR**

```SQL
SELECT *
FROM movies
WHERE year > 2014
   OR genre = 'action';
```

```SQL
SELECT *
FROM movies
WHERE year > 2014
   OR genre = 'action';

SELECT *
FROM movies
WHERE genre = 'romance'
   OR genre = 'comedy';
```
    
* **ORDER BY** sorts the result.
  * 永遠放在 `WHERE` 子句之後
  
```SQL
SELECT *
FROM movies
ORDER BY name;
```
    
  * 可以指定排序是遞增 `ASC` (預設是遞增) 或是遞減 `DESC`

```SQL
SELECT *
FROM movies
WHERE imdb_rating > 8
ORDER BY year DESC;
```

```SQL
SELECT name, year
FROM movies
ORDER BY name;

SELECT name, year, imdb_rating
FROM movies
ORDER BY imdb_rating DESC;
```

* **LIMIT** specifies the maximum number of rows that the query will return.

```SQL
SELECT *
FROM movies
LIMIT 10;

SELECT *
FROM movies
ORDER BY imdb_rating DESC
LIMIT 3;
```

* **CASE** creates different outputs.
  * `CASE` 是 SQL 的 if-else-then 敘述
  * 通常放在 `SELECT` 子句裡面
  * 記得要加上 `END` 然後可以 `AS` 成某個變數，供之後使用

```SQL
SELECT name,
 CASE
  WHEN imdb_rating > 8 THEN 'Fantastic'
  WHEN imdb_rating > 6 THEN 'Poorly Received'
  ELSE 'Avoid at All Costs'
 END
FROM movies;

SELECT name,
 CASE
  WHEN genre = 'romance' THEN 'Chill'
  WHEN genre = 'comedy' THEN 'Chill'
  ELSE 'Intense'
 END AS 'Mood'
FROM movies;
```
    
# Learn SQL - Aggregate Functions

* **COUNT()**: count the number of rows

```SQL
SELECT COUNT(*)
FROM table_name;

SELECT COUNT(*)
FROM fake_apps;

SELECT COUNT(*)
FROM fake_apps
WHERE price = 0;
```
  
* **SUM()**: the sum of the values in a column

```SQL
SELECT SUM(downloads)
FROM fake_apps;
```
  
* **MAX()** / **MIN()**: the largest / smallest value

```SQL
SELECT MAX(downloads)
FROM fake_apps;

SELECT MIN(downloads)
FROM fake_apps;

SELECT MAX(price)
FROM fake_apps;
```
  
* **AVG()**: the average of the values in a column

```SQL
SELECT AVG(downloads)
FROM fake_apps;
```
  
* **ROUND()**: round the values in the column

```SQL
SELECT ROUND(price, 0)
FROM fake_apps;

SELECT name, ROUND(price, 0)
FROM fake_apps;

SELECT ROUND(AVG(price), 2)
FROM fake_apps;
```
  
* **GROUP BY** is a clause used with aggregate functions to combine data from one or more columns.
  * `GROUP BY` 放在 `WHERE` 之後，`ORDER BY`, `LIMIT` 之前
  
```SQL
SELECT year,
 AVG(imdb_rating)
FROM movies
GROUP BY year
ORDER BY year;

SELECT price, COUNT(*) 
FROM fake_apps
GROUP BY price;

SELECT price, COUNT(*) 
FROM fake_apps
WHERE downloads > 20000
GROUP BY price;

SELECT category, SUM(downloads)
FROM fake_apps
GROUP BY category;
```
    
  * `GROUP BY` 的東西也可以做計算
  
```SQL
SELECT ROUND(imdb_rating),
  COUNT(name)
FROM movies
GROUP BY ROUND(imdb_rating)
ORDER BY ROUND(imdb_rating);
```
    
  * 可以用數字表示，數字編號是看 `SELECT` 那行，從 1 開始
  
```SQL
SELECT ROUND(imdb_rating),
  COUNT(name)
FROM movies
GROUP BY 1
ORDER BY 1;
```
`ROUND(imdb_rating)` 是 1，`COUNT(name)` 是 2

```SQL
SELECT category, 
  price,
  AVG(downloads)
FROM fake_apps
GROUP BY 1, 2;
```
`category` 是 1，`price` 是 2，`AVG(downloads)` 是 3

* **HAVING** limit the results of a query based on an aggregate property.
  * `HAVING` 其實和 `WHERE` 功能一樣，差別在於一般的條件用 `WHERE` ，有 aggregation function 的條件用 `HAVING`
  * `HAVING` 放在 `GROUP BY` 之後，`ORDER BY`, `LIMIT` 之前
  
```SQL
SELECT year,
  genre,
  COUNT(name)
FROM movies
GROUP BY 1, 2
HAVING COUNT(name) > 10;

SELECT price, count(*),
  ROUND(AVG(downloads))
FROM fake_apps
GROUP BY price;

SELECT price, 
  ROUND(AVG(downloads))
FROM fake_apps
GROUP BY price
HAVING COUNT(*) > 9;
```
    
# Learn SQL - Multiple Tables