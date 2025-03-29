# A. Pizza Metrics

**1. How many pizzas were ordered?**
```sql
SELECT
  COUNT(*) AS total_pizzas
FROM temp_customer_orders;
```
**Result:**
| total_pizzas |
| ------------ |
| 14           |
   
**2. How many unique customer orders were made?**
```sql
SELECT
  COUNT(DISTINCT(order_id)) AS unique_customer_orders
FROM temp_customer_orders;
```
**Result:**
| unique_customer_orders |
| ---------------------- |
| 10                     |
   
**3. How many successful orders were delivered by each runner?**
```sql
SELECT
  runner_id,
  COUNT(DISTINCT(order_id)) AS successful_orders
FROM temp_runner_orders
WHERE cancellation IS NULL
GROUP BY runner_id;
```
**Result:**
| runner_id | successful_orders |
| --------- | ----------------- |
| 1         | 4                 |
| 2         | 3                 |
| 3         | 1                 |

**4. How many of each type of pizza was delivered?**
```sql
SELECT
  pizza_name,
  COUNT(*) AS delivered_pizzas
FROM temp_customer_orders co JOIN pizza_runner.pizza_names 
ON co.pizza_id = pizza_names.pizza_id
JOIN temp_runner_orders ro
ON ro.order_id = co.order_id 
WHERE cancellation IS NULL
GROUP BY pizza_name;
```
**Result:**
| pizza_name | delivered_pizzas |
| ---------- | ---------------- |
| Vegetarian | 3                |
| Meatlovers | 9                |
   
**5. How many Vegetarian and Meatlovers were ordered by each customer?**
```sql
SELECT
  customer_id,
  pizza_name,
  COUNT(*) AS ordered_pizzas
FROM temp_customer_orders c JOIN pizza_runner.pizza_names p
ON c.pizza_id = p.pizza_id
GROUP BY customer_id, pizza_name
ORDER BY customer_id;
```
**Result:**
| customer_id | pizza_name | ordered_pizzas |
| ----------- | ---------- | -------------- |
| 101         | Meatlovers | 2              |
| 101         | Vegetarian | 1              |
| 102         | Meatlovers | 2              |
| 102         | Vegetarian | 1              |
| 103         | Meatlovers | 3              |
| 103         | Vegetarian | 1              |
| 104         | Meatlovers | 3              |
| 105         | Vegetarian | 1              |

**6. What was the maximum number of pizzas delivered in a single order?**
```sql
SELECT
  COUNT(*) AS max_pizzas_per_order
FROM temp_customer_orders co JOIN temp_runner_orders ro
ON co.order_id = ro.order_id
WHERE cancellation IS NULL
GROUP BY co.order_id
ORDER BY max_pizzas_per_order DESC
LIMIT 1;
```
**Result:**
| max_pizzas_per_order |
| -------------------- |
| 3                    |

**7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**
```sql
WITH flag_cte AS
(
SELECT
  co.order_id,
  co.customer_id,
  CASE WHEN exclusions IS NOT NULL OR extras IS NOT NULL THEN 1
  ELSE 0 END AS modified_flag,
  CASE WHEN exclusions IS NULL AND extras IS NULL THEN 1
  ELSE 0 END AS unmodified_flag
FROM temp_customer_orders co JOIN temp_runner_orders ro
ON co.order_id = ro.order_id
WHERE cancellation IS NULL
)
SELECT
  customer_id,
  SUM(modified_flag) AS modified_pizzas,
  SUM(unmodified_flag) AS unmodified_pizzas
FROM flag_cte f
GROUP BY customer_id
ORDER BY customer_id;    
```
**Result:**
| customer_id | modified_pizzas | unmodified_pizzas |
| ----------- | --------------- | ----------------- |
| 101         | 0               | 2                 |
| 102         | 0               | 3                 |
| 103         | 3               | 0                 |
| 104         | 2               | 1                 |
| 105         | 1               | 0                 |
    
**8. How many pizzas were delivered that had both exclusions and extras?**
```sql
SELECT
  COUNT(*) AS pizzas_w_exclusions_extras
FROM temp_customer_orders co JOIN temp_runner_orders ro
ON co.order_id = ro.order_id
WHERE cancellation IS NULL AND
      exclusions IS NOT NULL AND
      extras IS NOT NULL;
```
Result:
| pizzas_w_exclusions_extras |
| -------------------------- |
| 1                          |

**9. What was the total volume of pizzas ordered for each hour of the day?**
```sql
SELECT 
  EXTRACT(HOUR FROM order_time) AS hour,
  COUNT(*) AS total_pizzas
FROM temp_customer_orders
GROUP BY hour
ORDER BY hour;  
```
**Result:**
| hour | total_pizzas |
| ---- | ------------ |
| 11   | 1            |
| 13   | 3            |
| 18   | 3            |
| 19   | 1            |
| 21   | 3            |
| 23   | 3            |

**10. What was the volume of orders for each day of the week?**
```sql
SELECT 
  TO_CHAR(order_time, 'Day') AS day,
  COUNT(*) AS total_pizzas
FROM temp_customer_orders
GROUP BY day
ORDER BY day;
```
**Result:**
| day       | total_pizzas |
| --------- | ------------ |
| Friday    | 1            |
| Saturday  | 5            |
| Thursday  | 3            |
| Wednesday | 5            |
