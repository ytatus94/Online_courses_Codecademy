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

* 建立暫時的表格
```SQL
CREATE TEMPORARY TABLE table_name (
    SELECT statements
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
SELECT column_1, column_2, column_3
FROM table_name;
```

* **UPDATE** edits a row in a table.

```SQL
UPDATE celebs
SET age = 22
WHERE id = 1;

SELECT *
FROM celebs;
```

* **ALTER TABLE** changes an existing table.

```SQL
ALTER TABLE table_name ADD COLUMN
column_1 data_type;
```

```SQL
ALTER TABLE celebs ADD COLUMN
twitter_handle TEXT;

SELECT *
FROM celebs;
```

* **DELETE FROM** deletes rows from a table.
  
```SQL
UPDATE celebs
SET twitter_handle = '@taylorswift13'
WHERE id = 4;

DELETE FROM celebs
WHERE twitter_handle IS NULL;
SELECT *
FROM celebs;
```

* Constrains
  * 在建立表格時可以為每個欄位加上 constrains
    * 是 `PRIMARY KEY` 或是 `FOREIGN KEY`
      * `PRIMARY KEY` is a column that serves a unique identifier for the rows in the table.
      * `FOREIGN KEY` is a column that contains the primary key to another table.
    * 不允許空值 `NOT NULL`
    * 有預設值 `DEFAULT 'default_value'`
  * 在 SQL 中，空值是 `NULL`，可以用 `IS NULL` 和 `IS NOT NULL` 來判斷是否是空值

```SQL
CREATE TABLE awards (
  id INTEGER PRIMARY KEY,
  recipient TEXT NOT NULL,
  award_name TEXT DEFAULT "Grammy"
);
```

```SQL
CREATE TABLE celebs (
   id INTEGER PRIMARY KEY, 
   name TEXT UNIQUE,
   date_of_birth TEXT NOT NULL,
   date_of_death TEXT DEFAULT 'Not Applicable',
);
```

* **DROP TABLE** removes a table in a database

```SQL
DROP TABLE table_name;
```

## Queries

* **SELECT** is the clause we use every time we want to query information from a database.

```SQL
SELECT column1, column2 
FROM table_name;
```

```SQL
SELECT *
FROM movies;
```

```SQL
SELECT name, genre
FROM movies;

SELECT name, genre, year
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
  * **DISTINCT** 只對後面一個欄位起作用 

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
* `BETWEEN 'A' AND 'J'` 會選出 name 以 A, B, C, D, E, F, G, H, I 開頭的 (沒有 J 開頭的)
* `BETWEEN 1990 AND 1999` 會選出 year 是 1990, 1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998, 1999 的
    
```SQL
SELECT *
FROM movies
WHERE name BETWEEN 'D' AND 'G';

SELECT *
FROM movies
WHERE year BETWEEN 1970 AND 1979;
```
不包含 `G`，包含 `1979`
    
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
    * `CASE-WHEN-THEN-ELSE-END`
    * 可以放多個 `WHEN-THEN`，而且每個 `WHEN-THEN` 之間沒有逗號
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
    
## Aggregate Functions

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
  * 有 aggregate functions 就一定要有 `GROUP BY`
  * 有 `GROUP BY` 就一定要有 aggregate functions.
  
```SQL
SELECT year, AVG(imdb_rating)
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
SELECT ROUND(imdb_rating), COUNT(name)
FROM movies
GROUP BY ROUND(imdb_rating)
ORDER BY ROUND(imdb_rating);
```
    
  * 可以用數字表示，數字編號是看 `SELECT` 那行，從 1 開始
  
```SQL
SELECT ROUND(imdb_rating), COUNT(name)
FROM movies
GROUP BY 1
ORDER BY 1;
```
`ROUND(imdb_rating)` 是 1，`COUNT(name)` 是 2

```SQL
SELECT category, price, AVG(downloads)
FROM fake_apps
GROUP BY 1, 2;
```
`category` 是 1，`price` 是 2，`AVG(downloads)` 是 3

* **HAVING** limit the results of a query based on an aggregate property.
  * `HAVING` 其實和 `WHERE` 功能一樣，差別在於一般的條件用 `WHERE` ，有 aggregate function 的條件用 `HAVING`
  * `HAVING` 放在 `GROUP BY` 之後，`ORDER BY`, `LIMIT` 之前
  
```SQL
SELECT year, genre, COUNT(name)
FROM movies
GROUP BY 1, 2
HAVING COUNT(name) > 10;

SELECT price, COUNT(*), ROUND(AVG(downloads))
FROM fake_apps
GROUP BY price;

SELECT price, ROUND(AVG(downloads))
FROM fake_apps
GROUP BY price
HAVING COUNT(*) > 9;
```
    
## Multiple Tables

* **JOIN** will combine rows from different tables if the join condition is true.
  * 兩個表個中的欄位名稱可能會重複，用 `table_name.column_name` 可以指明是哪個表格的欄位
  * 預設 `JOIN` 就指的是 *inner join*，只有兩個表格都有的列才會結合再一起
    * `JOIN` 就是 inner join
    * outer join 有三種
      * `LEFT JOIN` = `LEFT OUTER JOIN`
      * `RIGHT JOIN` = `RIGHT OUTER JOIN`
      * `FULL JOIN` = `FULL OUTER JOIN`

```SQL
SELECT *
FROM orders
JOIN customers
  ON orders.customer_id = customers.customer_id;
```

```SQL
SELECT orders.order_id, customers.customer_name
FROM orders
JOIN customers
  ON orders.customer_id = customers.customer_id;
```

```SQL
-- First query
SELECT *
FROM orders
JOIN subscriptions
  ON orders.subscription_id = subscriptions.subscription_id;

-- Second query
SELECT *
FROM orders
JOIN subscriptions
  ON orders.subscription_id = subscriptions.subscription_id
WHERE subscriptions.description = 'Fashion Magazine';
```

```SQL
SELECT COUNT(*)
FROM newspaper;

SELECT COUNT(*)
FROM online;

SELECT COUNT(*)
FROM newspaper
JOIN online
  ON newspaper.id = online.id;
```

```SQL
SELECT *
FROM classes
JOIN students
  ON classes.id = students.class_id;
```

* **LEFT JOIN** will return every row in the left table, and if the join condition is not met, `NULL` values are used to fill in the columns from the right table.
  * 左邊的表格全部保留下來，右邊的表格只有在列與左邊表格相同時才結合。
  * 左邊表格有但是右邊表格沒有的列，結合後顯示 `NULL`

```SQL
SELECT *
FROM table1
LEFT JOIN table2
  ON table1.c2 = table2.c2;
```

```SQL
-- First query
SELECT *
FROM newspaper
LEFT JOIN online
  ON newspaper.id = online.id;
  
-- Second query
SELECT *
FROM newspaper
LEFT JOIN online
  ON newspaper.id = online.id
WHERE online.id IS NULL;
```

* *Primary key* is a column that serves a unique identifier for the rows in the table.

* *Foreign key* is a column that contains the primary key to another table.

* **CROSS JOIN** lets us combine all rows of one table with all rows of another table.
  * 左邊表格每一列，會與右邊表格每一列配對，結合起來。如果左邊有兩列右邊有三列，`CROSS JOIN` 之後的表格就會有 2 x 3 = 6 列

```SQL
SELECT shirts.shirt_color, pants.pants_color
FROM shirts
CROSS JOIN pants;
```

```SQL
-- First query
SELECT COUNT(*)
FROM newspaper
WHERE start_month <= 3
  AND end_month >= 3;
  
-- Second query  
SELECT *
FROM newspaper
CROSS JOIN months;

-- Third query
SELECT *
FROM newspaper
CROSS JOIN months
WHERE start_month <= month
  AND end_month >= month;

-- Fourth query
SELECT months.month, COUNT(*)
FROM newspaper
CROSS JOIN months
WHERE start_month < month
  AND end_month > month
GROUP BY months.month;
```

* **UNION** stacks one dataset on top of another.
  * 將兩表格上下疊再一起，要做 `UNION` 時，兩個表格的欄位要一樣
  * **UNION** 會把重複的 row 刪掉，只會保留一個 row
  * **UNION ALL** 不會把重複的 row 刪掉，會全部保留

```SQL
SELECT *
FROM table1
UNION
SELECT *
FROM table2;
```

```SQL
SELECT *
FROM newspaper
UNION
SELECT *
FROM online;
```

* **WITH** allows us to define one or more temporary tables that can be used in the final query.
  * `WITH` 內先做另一個查詢，產生一個暫時的表格。可以將暫時的表格與其他表格做 `JOIN` 再做其他查詢
  * `WITH` 子句叫做 common table expression (CTE)

```SQL
WITH previous_results AS (
   SELECT ...
   ...
   ...
   ...
)
SELECT *
FROM previous_results
JOIN customers
  ON _____ = _____;
```

```SQL
WITH previous_query AS (
  SELECT customer_id, COUNT(subscription_id) AS subscriptions
  FROM orders
  GROUP BY customer_id
)
SELECT customers.customer_name, previous_query.subscriptions
FROM previous_query
JOIN customers
  ON previous_query.customer_id = customers.customer_id;
```

  * 如果有兩個以上的 CTE 時就用逗號隔開

```SQL
WITH cte1 AS (
    SELECT ....
),
cte2 AS (
    SELECT ...
)
```
