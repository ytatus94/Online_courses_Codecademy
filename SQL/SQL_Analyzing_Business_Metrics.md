# SQL: Analyzing Business Metrics

## Advanced Aggregates

### Example
在這個範例中有兩個表格 `orders` 和 `order_items`

* `orders` 有 `id`, `ordered_at`, `delivered_at`, `delivered_to` 四個欄位
* `order_items` 有 `id`, `order_id`, `name`, `amount_paid` 四個欄位

從 `orders` 表格中選擇全部欄位，依照 `id` 排序後只顯示前 100 列

```SQL
SELECT *
FROM orders
ORDER BY id
LIMIT 100;
```

從 `order_items` 表格中選擇全部欄位，依照 `id` 排序後只顯示前 100 列

```SQL
SELECT *
FROM order_items
ORDER BY id
LIMIT 100;
```

從 `orders` 表格選擇時間戳記，只顯示日期的部分，然後依照日期遞增排序，只顯示前 100 列

* 此時表格中可能會有重複的日期。

``` SQL
SELECT DATE(ordered_at)
FROM orders
ORDER BY 1
LIMIT 100;
```

從 `orders` 表格選擇時間戳記，只顯示日期的部分，然後把相同的日期群組起來，計算同一個日期出現了幾次，並將結果依照日期遞增排序，只顯示前 100 列

```SQL
SELECT DATE(ordered_at), COUNT(1)
FROM orders
GROUP BY 1
ORDER BY 1
LIMIT 100;
```

把 `orders` 和 `order_items` 兩個表格用 inner join 依照 `orders.id` 和 `order_items.order_id` 結合起來，顯示日期還有當日銷售總額，並將結果依照日期排序。

* `ROUND(SUM(amount_paid), 2)` 是把銷售金額加起來，再用 `ROUND()` 做四捨五入取到小數點第二位

```SQL
SELECT DATE(ordered_at), 
  ROUND(SUM(amount_paid), 2)
FROM orders
JOIN order_items
  ON orders.id = order_items.order_id
GROUP BY 1
ORDER BY 1;
```

同上，但是這次只要看 kale-smoothie 的每日銷售總額

```SQL
SELECT DATE(ordered_at), 
  ROUND(SUM(amount_paid), 2)
FROM orders
JOIN order_items
  ON orders.id = order_items.order_id
WHERE name = 'kale-smoothie'
GROUP BY 1
ORDER BY 1;
```

看 `order_items` 表格中，每一項產品的銷售總額，並且依照銷售總額遞減排序

```SQL
SELECT name, ROUND(SUM(amount_paid), 2)
FROM order_items
GROUP BY name
ORDER BY 2 DESC;
```

看 `order_items` 表格中，每一項產品的銷售百分比，並且依照銷售百分比遞減排序

* 因為要計算銷售百分比，所以要知道全部的銷售總額。因此在分母的部分另寫一個 subquery 來計算全部的銷售總額
  * 用 100.0 變成浮點數來計算，才不會出問題 

```SQL
SELECT name, ROUND(SUM(amount_paid) /
  (SELECT SUM(amount_paid) FROM order_items) * 100.0, 2) AS pct
FROM order_items
GROUP BY 1
ORDER BY 2 DESC;
```

在 `order_items` 表格中新增一個 `category` 欄位，將結果依照 `id` 排序，只顯示前 100 列

* `category` 的值是依照 `name` 欄位做判斷

```SQL
SELECT *,
  CASE name
    WHEN 'kale-smoothie'    THEN 'smoothie'
    WHEN 'banana-smoothie'  THEN 'smoothie'
    WHEN 'orange-juice'     THEN 'drink'
    WHEN 'soda'             THEN 'drink'
    WHEN 'blt'              THEN 'sandwich'
    WHEN 'grilled-cheese'   THEN 'sandwich'
    WHEN 'tikka-masala'     THEN 'dinner'
    WHEN 'chicken-parm'     THEN 'dinner'
    ELSE 'other'
  END AS category
FROM order_items
ORDER BY id
LIMIT 100;
```

計算每個 `category` 的銷售百分比，並將結果依照百分比遞減排序

* 因為要計算百分比，除了每個 `category` 銷售總額外，還要知道全部的銷售總額。全部的銷售總額就另外寫一個 subquery 來計算。

```SQL
SELECT
  CASE name
    WHEN 'kale-smoothie'    THEN 'smoothie'
    WHEN 'banana-smoothie'  THEN 'smoothie'
    WHEN 'orange-juice'     THEN 'drink'
    WHEN 'soda'             THEN 'drink'
    WHEN 'blt'              THEN 'sandwich'
    WHEN 'grilled-cheese'   THEN 'sandwich'
    WHEN 'tikka-masala'     THEN 'dinner'
    WHEN 'chicken-parm'     THEN 'dinner'
    ELSE 'other'
  END AS category, 
  ROUND(1.0 * SUM(amount_paid) /
    (SELECT SUM(amount_paid) FROM order_items) * 100, 2) AS pct
FROM order_items
GROUP BY 1
ORDER BY 2 DESC;
```

計算每一樣商品的銷售數量，依照商品名稱排序

```SQL
SELECT name, COUNT(DISTINCT order_id)
FROM order_items
GROUP BY 1
ORDER BY 1;
```

計算每一樣商品重複的下訂單的比例，依照重複的下訂單的比例排序
* 不是很懂為什麼是除以 COUNT(DISTINCT orders.delivered_to)`

```SQL
SELECT name, ROUND(1.0 * COUNT(DISTINCT order_id) /
  COUNT(DISTINCT orders.delivered_to), 2) AS reorder_rate
FROM order_items
JOIN orders
  ON orders.id = order_items.order_id
GROUP BY 1
ORDER BY 2 DESC;
```

### 其他補充說明

* Timestamps 的格式是 YYYY-MM-DD hh:mm:ss
  * 例如： 2015-01-05 14:43:31
  * `DATE(timestamps)` 只顯示日期的部分
* This will treat all rows as a single group, and return one row in the result set - the total count.
  * 這邊 `COUNT(1)` 就和 `COUNT(*)` 是一樣的

```SQL
SELECT COUNT(1)
FROM users;
```

```SQL
SELECT DATE(created_at), COUNT(1)
FROM users
GROUP BY DATE(created_at)
```

* A subquery can perform complicated calculations and create filtered or aggregate tables on the fly.
  * Subqueries are useful when you want to perform an aggregation outside the context of the current query. 

* `CASE` 語法

```SQL
CASE {condition}
  WHEN {value1} THEN {result1}
  WHEN {value2} THEN {result2}
  ELSE {result3}
END
```

* Data aggregation is the grouping of data in summary form.
* Daily Count is the count of orders in a day.
* Daily Revenue Count is the revenue on orders per day.
* Product Sum is the total revenue of a product.
* Reorder Rate is the ratio of the total number of orders to the number of people making orders.


## Common Metrics

* **KPIs**: key performance indicators
  * Key Performance Indicators are high level health metrics for a business.
* **Daily Revenue** is the sum of money made per day.
* **Daily Active Users (DAU)** are the number of unique users seen each day
  * Weekly Active Users (WAU) and Monthly Active Users (MAU) are in the same family.
* **Daily Average Revenue Per Purchasing User (ARPPU)** is the average amount of money spent by purchasers each day.
  * Daily ARPPU = sum of revenue / number of purchasers per day.
  * ARPPU increases if purchasers are spending more money.
* **Daily Average Revenue Per User (ARPU)** is the average amount of money across all users.
  * Daily ARPU = revenue / number of players, per-day.
  * ARPU increases if more players are choosing to purchase, even if the purchase size stays consistent.
* **1 Day Retention** is defined as the number of players from Day N who came back to play again on Day N+1.



### Example
在這個範例中有兩個表格 `purchases` 和 `gameplays`

* `purchases` 有 `id`, `user_id`, `price`, `refounded_at`, `created_at` 五個欄位
* `gameplays` 有 `id`, `user_id`, `created_at`, `platform` 四個欄位

顯示 `purchases` 表格的所有欄位，依照 `id` 遞增排序，只顯示前 10 列

```SQL
SELECT * 
FROM purchases
ORDER BY id 
LIMIT 10;
```

顯示 `gameplays` 表格的所有欄位，依照 `id` 遞增排序，只顯示前 10 列

```SQL
SELECT * 
FROM gameplays
ORDER BY id 
LIMIT 10;
```

看 Daily Revenue (每天賺到的錢) 

```SQL
SELECT
  DATE(created_at),
  ROUND(SUM(price), 2)
FROM purchases
GROUP BY 1
ORDER BY 1;
```

* 但是 `purchases` 有一個欄位叫做 `refunded_at`，只有 `refunded_at` 是空的，才代表沒退錢，所以要排除掉 `refunded_at` 有值的那一列

```SQL
SELECT
  DATE(created_at),
  ROUND(SUM(price), 2) AS daily_rev
FROM purchases
WHERE refunded_at IS NULL
GROUP BY 1
ORDER BY 1;
```

計算每日登入的用戶有幾個 (DAU)

* 一個用戶可能每天登入很多次，所以要用 `DISTTICT` 才不會重複計算

```SQL
SELECT
  DATE(created_at), 
  COUNT(DISTINCT user_id) as dau
FROM gameplays
GROUP BY 1
ORDER BY 1;
```

* 依照日期和作業系統分群組並且排序

```SQL
SELECT
  DATE(created_at), 
  platform,
  COUNT(DISTINCT user_id) as dau
FROM gameplays
GROUP BY 1, 2
ORDER BY 1, 2;
```

看平均每個有購買的使用者每天花多少錢 (ARPPU)

* 每天總收入 / 每天多少個有購買的使用者
* 使用者是要算有花錢購買的，所以看 `purchases` 表格，因為這個表格內的才是有花錢購買的使用者

```SQL
SELECT
  DATE(created_at),
  ROUND(SUM(price) / COUNT(DISTINCT user_id), 2) AS arppu
FROM purchases
WHERE refunded_at IS NULL
GROUP BY 1
ORDER BY 1;
```

看平均每個使用者花多少錢 (ARPU)

* 使用者 = 有花錢購買的玩家 + 免費玩家
* ARPU = 營收 / 使用者數目
* 首先先看每日營收
  * `WITH` 內的 subquery 就是每日營收了，要用 `WITH` 是因為之後還有其他需求的關係

```SQL
WITH daily_revenue AS (
  SELECT
    DATE(created_at) AS dt,
    ROUND(SUM(price), 2) AS rev
  FROM purchases
  WHERE refunded_at IS NULL
  GROUP BY 1
)
SELECT * 
FROM daily_revenue 
ORDER BY dt;
```

* 再看看每天登入的使用者有幾個
  * 第一個 subquery 是每日營收，第二個 subquery 是每天登入的使用者 (DAU)

```SQL
WITH daily_revenue AS (
  SELECT
    DATE(created_at) AS dt,
    ROUND(SUM(price), 2) AS rev
  FROM purchases
  WHERE refunded_at IS NULL
  GROUP BY 1
), 
daily_players AS (
  SELECT
    DATE(created_at) AS dt,
    COUNT(DISTINCT user_id) AS players
  FROM gameplays
  GROUP BY 1
)
SELECT *
FROM daily_players
ORDER BY dt;
```

* 計算 ARPU

```SQL
WITH daily_revenue AS (
  SELECT
    DATE(created_at) AS dt,
    ROUND(SUM(price), 2) AS rev
  FROM purchases
  WHERE refunded_at IS NULL
  GROUP BY 1
), 
daily_players AS (
  SELECT
    DATE(created_at) AS dt,
    COUNT(DISTINCT user_id) AS players
  FROM gameplays
  GROUP BY 1
)
SELECT
  daily_revenue.dt,
  daily_revenue.rev / daily_players.players
FROM daily_revenue
JOIN daily_players USING (dt);
```

看隔天玩家的回鍋率 (1 Day Retention)

* 先看每天多少玩家

```SQL
SELECT
  DATE(created_at) AS dt,
  user_id
FROM gameplays AS g1
ORDER BY dt
LIMIT 100;
```

* 用 *self-join* 把 `gameplayes` 和自己結合
  * *self-join* 就是把表格自己和自己結合

```SQL
SELECT
  DATE(g1.created_at) AS dt,
  g1.user_id
FROM gameplays AS g1
JOIN gameplays AS g2
  ON g1.user_id = g2.user_id
ORDER BY 1
LIMIT 100;
```

* 同上，但是在結合表格時，加上額外的條件，要求第二個表格和第一個表格的日期差一天
  * `DATETIME(g2.created_at, '-1 day')` 表示 `g2.created_at` 的日期減一天

```SQL
SELECT
  DATE(g1.created_at) AS dt,
  g1.user_id,
  g2.user_id
FROM gameplays AS g1
JOIN gameplays AS g2
  ON g1.user_id = g2.user_id
  AND DATE(g1.created_at) = DATE(DATETIME(g2.created_at, '-1 day'))
ORDER BY 1
LIMIT 100;
```

* 要用 *inner-join* 才正確

```SQL
SELECT
  DATE(g1.created_at) AS dt,
  COUNT(DISTINCT g1.user_id) AS total_users,
  COUNT(DISTINCT g2.user_id) AS retained_users
FROM gameplays AS g1
LEFT JOIN gameplays AS g2
  ON g1.user_id = g2.user_id
  AND date(g1.created_at) = date(DATETIME(g2.created_at, '-1 day'))
GROUP BY 1
ORDER BY 1
LIMIT 100;
```

* 玩家的回鍋率 = 第 N+1 天的玩家數目 / 第 N 天的玩家數目

```SQL
SELECT
  DATE(g1.created_at) AS dt,
  ROUND(100 * COUNT(DISTINCT g2.user_id) /
    COUNT(DISTINCT g1.user_id)) AS retention
FROM gameplays AS g1
LEFT JOIN gameplays AS g2
  ON g1.user_id = g2.user_id
  AND date(g1.created_at) = date(DATETIME(g2.created_at, '-1 day'))
GROUP BY 1
ORDER BY 1
LIMIT 100;
```

### 其他補充說明
* `WITH` 子句又叫做 CTEs (Common Table Expressions)
  * 語法

```SQL
WITH {subquery_name} AS (
    {subquery_body}
  )
SELECT ...
FROM {subquery_name}
WHERE ...
```

* 當兩個表格要結合的欄位有相同的名字時，可以用 `USING` 取代 `ON`
  * 語法：`JOIN table_name USING (column)` 
  * 範例：

```SQL
FROM daily_revenue
JOIN daily_players USING (dt);
```

上面和下面等價

```SQL
FROM daily_revenue
JOIN daily_players
  ON daily_revenue.dt = daily_players.dt;
```
