# SQL: Table Transformation

## Subqueries

* Subqueries 又叫做 inner queries 或 nested queries
  * 把 query 巢狀放在另一個 query 內
* Subquery 分成兩種
  * A non-correlated subquery：inner 和 outer query 互相獨立
  * A correlated subquery：inner 和 outer query 相關
* The order of operations in a correlated subquery is as follows:
  * A row is processed in the outer query.
  * Then, for that particular row in the outer query, the subquery is executed.
* Subqueries 可以放在 SELECT 裡面, FROM 裡面, WHERE 裡面 

### Example 1.
從 `flights` 表格中選擇 10 筆資料

```SQL
SELECT * 
FROM flights
LIMIT 10;
```
從 `airports` 表格中選擇海拔高於 2000m 的

```SQL
SELECT code 
FROM airports 
WHERE elevation > 2000;
```
選擇來自海拔高於 2000m 的機場的飛機，就是將兩個搜尋用 subquery 結合再一起

```SQL
SELECT * 
FROM flights 
WHERE origin in (
    SELECT code 
    FROM airports 
    WHERE elevation > 2000);
```
這兩個表格是獨立的，所以這是屬於 non-correlated subquery

同理，選擇來自海拔小於 2000m 的機場的飛機

```SQL
SELECT * 
FROM flights 
WHERE origin in (
    SELECT code 
    FROM airports 
    WHERE elevation < 2000);
```

### Example 2.
選擇來自於機場種類 (`fac_type`) 是 `SEAPLANE_BASE` 的機場的飛機

```SQL
SELECT * 
FROM flights 
WHERE origin in (
    SELECT code 
    FROM airports 
    WHERE fac_type = 'SEAPLANE_BASE');
```
選擇來自於機場的 Federal Aviation Administration region (`faa_region`) 是 `ASO` 的機場的飛機

```SQL
SELECT * 
FROM flights 
WHERE origin in (
    SELECT code 
    FROM airports 
    WHERE faa_region = 'ASO');
```

### Example 3.
找某一個月份的某一個 weekday 的平均飛機數目

1. 先在 inner query 中找出每天飛機的數目
2. 計算 weekday 的平均數目

```SQL
SELECT a.dep_month,
       a.dep_day_of_week,
       AVG(a.flight_count) AS average_flights
FROM (
      SELECT dep_month,
             dep_day_of_week,
             dep_date,
             COUNT(*) AS flight_count
      FROM flights
      GROUP BY 1,2,3
     ) a
GROUP BY 1,2
ORDER BY 1,2;
```
找某一個月份的某一個 weekday 的平均的飛行距離.

1. 先在 inner query 中找出每天飛機的飛行距離
2. 再用 1 的結果計算平均飛行距離，因為用 `dep_month` 和 `dep_day_of_week` 分群，所以會用分群後的資料來計算平均

```SQL
SELECT a.dep_month,
       a.dep_day_of_week,
       AVG(a.flight_distance) AS average_distance
FROM (
      SELECT dep_month,
             dep_day_of_week,
             dep_date,
             sum(distance) AS flight_distance
      FROM flights
      GROUP BY 1,2,3
     ) a
GROUP BY 1,2
ORDER BY 1,2;
```

### Example 4.
找出飛機的飛行距離比同公司的飛機平均飛行距離還大的

```SQL
SELECT id
FROM flights AS f
WHERE distance > (
    SELECT AVG(distance)
    FROM flights
    WHERE carrier = f.carrier);
```
同理，找出飛機的飛行距離比同公司的飛機平均飛行距離還小的

```SQL
SELECT id
FROM flights AS f
WHERE distance < (
    SELECT AVG(distance)
    FROM flights
    WHERE carrier = f.carrier);
```

### Example 5.
假設 `flights.id` 是遞增的，那下面的方式可以顯示同一家航空公司的第幾架次班機。

```SQL
SELECT carrier, id,
    (SELECT COUNT(*)
     FROM flights f
     WHERE f.id < flights.id
     AND f.carrier=flights.carrier) + 1
     AS flight_sequence_number
FROM flights;
```

假設 `flights.id` 是遞增的，那下面的方式可以顯示同一個機場的第幾架次班機。

```SQL
SELECT origin, id,
    (SELECT COUNT(*)
     FROM flights f
     WHERE f.id < flights.id
     AND f.origin=flights.origin) + 1
     AS flight_sequence_number
FROM flights;
```

## Set operations
* Merge 表格有兩種方式：
  1. Merge the rows 是用 **JOIN**
  2. Merge the columns 是用 **UNION**
     * `UNION` 就是把一個表格疊在另一個上面
     * 用 `UNION` 時，`SELECT` 列出的欄位名字與順序要相同
     * `UNION` 只選出 distinct values
     * **UNION ALL** 會保留 duplicate value
* **INTERSECT** 列出兩個 `SELECT` 共同的結果
* **EXCEPT** 列出第一個 `SELECT` 的結果中但是不包含在第二個的 `SELECT` 結果內的

### Example 1.

```SQL
SELECT brand
FROM legacy_products
UNION
SELECT brand
FROM new_products;
```

### Example 2.

```SQL
SELECT COUNT(*) FROM (
  SELECT id, sale_price FROM order_items
  UNION ALL
  SELECT id, sale_price FROM order_items_historic) AS a;
```

```SQL
SELECT id, AVG(a.sale_price) FROM (
  SELECT id, sale_price FROM order_items
  UNION ALL
  SELECT id, sale_price FROM order_items_historic) AS a 
  GROUP BY 1;
```

### Example 3.
列出在 `new_products` 內也在 `legacy_products` 內的

```SQL
SELECT category FROM new_products
INTERSECT
SELECT category FROM legacy_products;
```

### Example 4.
列出在 `legacy_products` 但不包含在 `new_products` 內的

```SQL
SELECT category FROM legacy_products
EXCEPT
SELECT category FROM new_products;
```

## Conditional Aggregates

### Example 1.
計算 flights 表格共有幾筆資料

```SQL
SELECT COUNT(*) FROM flights;
```

### Example 2.
* 用 **IS NULL**, **IS NOT NULL** 來判斷是否為空

計算有幾筆資料的 `arr_time` 不是空的，且目的地是 `ATL`

```SQL
SELECT COUNT(*) FROM flights
WHERE arr_time IS NOT NULL AND destination = 'ATL';
```

### Example 3.
**CASE WHEN THEN ELSE END** 就是 SQL 中的 if-then-else

* 一定要有 **END**
* **ELSE** 可有可不有，沒有時就是 NULL
* 可以寫在 `SELECT` 裡面，aggregation function 裡面

```SQL
SELECT
    CASE
        WHEN elevation < 500 THEN 'Low'
        WHEN elevation BETWEEN 500 AND 1999 THEN 'Medium'
        WHEN elevation >= 2000 THEN 'High'
        ELSE 'Unknown'
    END AS elevation_tier
    , COUNT(*)
FROM airports
GROUP BY 1;
```

```SQL
SELECT
    CASE
        WHEN elevation < 250 THEN 'Low'
        WHEN elevation BETWEEN 250 AND 1749 THEN 'Medium'
        WHEN elevation >= 1750 THEN 'High'
        ELSE 'Unknown'
    END AS elevation_tier
    , COUNT(*)
FROM airports
GROUP BY 1;
```

```SQL
SELECT state, 
       COUNT(CASE WHEN elevation >= 2000 THEN 1 ELSE NULL END) AS count_high_elevation_aiports 
FROM airports 
GROUP BY state;
```

```SQL
SELECT state, 
       COUNT(CASE WHEN elevation < 1000 THEN 1 ELSE NULL END) AS count_low_elevation_airports 
FROM airports 
GROUP BY state;
```

### Example 4.
想比較全部飛機的總飛行距離和特定某家航空公司的總飛行距離

```SQL
SELECT origin,
       SUM(distance) AS total_flight_distance,
       SUM(CASE WHEN carrier = 'UA' THEN distance ELSE 0 END) AS total_united_flight_distance 
FROM flights 
GROUP BY origin;
```

```SQL
SELECT origin,
       SUM(distance) AS total_flight_distance,
       SUM(CASE WHEN carrier = 'DL' THEN distance ELSE 0 END) AS total_delta_flight_distance 
FROM flights 
GROUP BY origin;
```

### Example 5.
想知道特定某家航空公司的總飛行距離佔全部飛機的總飛行距離的多少百分比

```SQL
SELECT origin, 
       100.0*(SUM(CASE WHEN carrier = 'UN' THEN distance ELSE 0 END)/SUM(distance)) AS percentage_flight_distance_from_united
FROM flights 
GROUP BY origin;
```

```SQL
SELECT origin,
       100.0*(SUM(CASE WHEN carrier = 'DL' THEN distance ELSE 0 END)/SUM(distance)) AS percentage_flight_distance_from_delta
FROM flights
GROUP BY origin;
```
想知道海拔高於 2000m 的機場佔全部機場的多少百分比

```SQL
SELECT state,
       100.0 * sum(CASE WHEN elevation >= 2000 THEN 1 ELSE 0 END) / count(*) AS percentage_high_elevation_airports
FROM airports
GROUP BY state;
```

## Date, Number, and String Functions

時間的格式有：

* YYYY-MM-DD
* YYYY-MM-DD hh:mm:ss

### Example 1.

```SQL
SELECT DATETIME(manufacture_time)
FROM baked_goods;
```

```SQL
SELECT DATETIME(delivery_time) 
FROM baked_goods;
```
用 `DATE()` 只顯示日期

```SQL
SELECT DATE(manufacture_time), COUNT(*) AS count_baked_goods
FROM baked_goods
GROUP BY DATE(manufacture_time);
```
用 `TIME()` 只顯示時間

```SQL
SELECT TIME(manufacture_time), COUNT(*) AS count_baked_goods
FROM baked_goods
GROUP BY TIME(manufacture_time);
```

```SQL
SELECT DATE(delivery_time), COUNT(*) AS count_baked_goods
FROM baked_goods
GROUP BY DATE(delivery_time);
```

### Example 2.

改變傳回的時間戳記：傳回 time1 之後的 2 天 3 小時 40 分鐘

```SQL
DATETIME(time1, '+3 hours', '40 minutes', '2 days');
```
傳回 `manufacture_time` 之後的 1 天 2 小時 30 分鐘

```SQL
SELECT DATETIME(manufacture_time, '+2 hours', '30 minutes', '1 day') AS inspection_time
FROM baked_goods;
```
傳回 `delivery_time` 之後的 2 天 5 小時 20 分鐘

```SQL
SELECT DATETIME(delivery_time, '+5 hours', '20 minutes', '2 days') AS package_time
FROM baked_goods;
```

### Example 3.

* `SELECT (number1 + number2);` 傳回相加的結果，可以用加減乘除
* `SELECT CAST(number1 AS REAL) / number3;` 先將 `number1` 轉換成 `REAL` 型態
* `SELECT ROUND(number, precision);` 傳回四捨五入後的結果

```SQL
SELECT ROUND(ingredients_cost, 4) AS rounded_cost
FROM baked_goods;
```

```SQL
SELECT ROUND(distance, 2) AS distance_from_market
FROM bakeries;
```

### Example 4.

* `MAX(n1,n2,n3,...)`: 傳回最大值
* `MIN(n1,n2,n3,...)`: 傳回最小值

```SQL
SELECT id, MAX(ingredients_cost, packaging_cost)
FROM baked_goods;
```

```SQL
SELECT id, MIN(cook_time, cool_down_time)
FROM baked_goods;
```

### Example 5.

用 `|| ' ' ||` 來連結字串

在這邊 `||` 就相當於 python 字串相加時的 `+`，`string1 || ' ' || string2` 就相當於 python 中的 string1 + ' ' + string2

```SQL
SELECT city || ' ' || state AS location
FROM bakeries;
```

```SQL
SELECT first_name || ' ' || last_name AS full_name
FROM bakeries;
```

### Example 6.
用 `REPLACE(string,from_string,to_string)` 來取代字串

```SQL
SELECT id, REPLACE(ingredients,'_',' ') AS item_ingredients
FROM baked_goods;
```

```SQL
SELECT REPLACE(ingredients,'enriched_',' ') as item_ingredients
FROM baked_goods;
```