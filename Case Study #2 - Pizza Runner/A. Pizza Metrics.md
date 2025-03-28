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
```
**Result:**
   
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
FROM temp_customer_orders
GROUP BY order_id
ORDER BY max_pizzas_per_order DESC
LIMIT 1;
```
**Result:**
| max_pizzas_per_order |
| -------------------- |
| 3                    |

**7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**
```sql
```
**Result:**
    
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
```
**Result:**

**10. What was the volume of orders for each day of the week?**
```sql
```
**Result:**
