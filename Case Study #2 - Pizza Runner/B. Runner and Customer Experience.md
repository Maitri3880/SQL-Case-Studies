# B. Runner and Customer Experience

**1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)**
```sql
SELECT 
  DATE_TRUNC('week', registration_date)::DATE AS week_start_date,
  (DATE_TRUNC('week', registration_date) + INTERVAL '6 days')::DATE AS week_end_date,
  COUNT(*) AS no_of_runners
FROM pizza_runner.runners
GROUP BY DATE_TRUNC('week', registration_date)
ORDER BY week_start_date;
```
**Result:**
| week_start_date | week_end_date | no_of_runners |
| --------------- | ------------- | ------------- |
| 2020-12-28      | 2021-01-03    | 2             |
| 2021-01-04      | 2021-01-10    | 1             |
| 2021-01-11      | 2021-01-17    | 1             |

**2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?**
```sql
WITH mins_cte AS (
  SELECT
    runner_id,
    co.order_id,
    EXTRACT(MINUTE FROM age(pickup_time, order_time)) AS minutes
  FROM temp_customer_orders co
  JOIN temp_runner_orders ro
    ON ro.order_id = co.order_id 
)
SELECT 
  runner_id,
  ROUND(AVG(minutes)::NUMERIC, 1) AS avg_pickup_time
FROM mins_cte 
GROUP BY runner_id
ORDER BY runner_id;
```
**Result:**
| runner_id | avg_pickup_time        |
| --------- | ---------------------- |
| 1         | 15.3                   |
| 2         | 23.4                   |
| 3         | 10.0                     |

**3. Is there any relationship between the number of pizzas and how long the order takes to prepare?**
```sql
WITH mins_cte AS (
  SELECT 
    co.order_id,
    COUNT(pizza_id) AS pizza_count,
    EXTRACT(MINUTE FROM age(pickup_time, order_time)) AS minutes
  FROM temp_customer_orders co 
  JOIN temp_runner_orders ro ON ro.order_id = co.order_id 
  GROUP BY co.order_id, pickup_time, order_time
)
SELECT 
  pizza_count,
  ROUND(AVG(minutes)::NUMERIC, 1) AS avg_minutes_taken
FROM mins_cte
GROUP BY pizza_count
ORDER BY pizza_count;
```
**Result:**
| pizza_count | avg_minutes_taken |
| ----------- | ----------------- |
| 1           | 12.0              |
| 2           | 18.0              |
| 3           | 29.0              |

**4. What was the average distance travelled for each customer?**
```sql
SELECT
  customer_id,
  ROUND(AVG(distance)::NUMERIC, 1) AS avg_distance
FROM temp_runner_orders ro JOIN temp_customer_orders co
ON ro.order_id = co.order_id
GROUP BY customer_id
ORDER BY customer_id;
```
**Result:**
| customer_id | avg_distance |
| ----------- | ------------ |
| 101         | 20.0         |
| 102         | 16.7         |
| 103         | 23.4         |
| 104         | 10.0         |
| 105         | 25.0         |

**5. What was the difference between the longest and shortest delivery times for all orders?**
```sql
SELECT
  MAX(duration) - MIN(duration) AS delivery_time_difference
FROM temp_runner_orders;
```
**Result:**
| delivery_time_difference |
| ------------------------ |
| 30                       |

**6. What was the average speed for each runner for each delivery and do you notice any trend for these values?**
```sql
SELECT
  ro.runner_id,
  ROUND(AVG(60*(ro.distance/ro.duration))::NUMERIC, 2) AS avg_speed
FROM temp_runner_orders ro
WHERE cancellation IS NULL
GROUP BY ro.runner_id
ORDER BY ro.runner_id;
```
**Result:**
| runner_id | avg_speed |
| --------- | --------- |
| 1         | 45.54     |
| 2         | 62.90     |
| 3         | 40.00     |

**7. What is the successful delivery percentage for each runner?**
```sql
SELECT
  runner_id,
  ROUND( COUNT(CASE WHEN cancellation IS NULL THEN 1 END)::NUMERIC /
         COUNT(order_id) * 100
  , 1) AS successful_delivery_pct
FROM temp_runner_orders
GROUP BY runner_id
ORDER BY runner_id;
```
**Result:**
| runner_id | successful_delivery_pct |
| --------- | ----------------------- |
| 1         | 100.0                   |
| 2         | 75.0                    |
| 3         | 50.0                    |
