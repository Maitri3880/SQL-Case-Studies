# B. Runner and Customer Experience

**1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)**
```sql
```
**Result:**

**2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?**
```sql
WITH mins_cte AS
(
  SELECT
    runner_id,
    co.order_id,
    EXTRACT(MINUTE FROM age(pickup_time, order_time)) AS minutes
  FROM temp_customer_orders co JOIN temp_runner_orders ro
  ON ro.order_id = co.order_id 
)
SELECT 
  runner_id,
  AVG(minutes) AS avg_minutes
FROM mins_cte 
GROUP BY runner_id
ORDER BY runner_id;
```
**Result:**
| runner_id | avg_minutes        |
| --------- | ------------------ |
| 1         | 15.333333333333334 |
| 2         | 23.4               |
| 3         | 10                 |

**3. Is there any relationship between the number of pizzas and how long the order takes to prepare?**
```sql
```
**Result:**

**4. What was the average distance travelled for each customer?**
```sql
```
**Result:**

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
```
**Result:**

**7. What is the successful delivery percentage for each runner?**
```sql
```
**Result:**

