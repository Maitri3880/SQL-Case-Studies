# D. Pricing and Ratings

**1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?**
```sql
SELECT
  SUM(CASE WHEN pn.pizza_name = 'Meatlovers' THEN 12
      ELSE 10 END) AS total_earnings
FROM temp_customer_orders co JOIN temp_runner_orders ro
ON co.order_id = ro.order_id
JOIN pizza_names pn
ON pn.pizza_id = co.pizza_id
WHERE ro.cancellation IS NULL;
```
**Result:**
| total_earnings |
| -------------- |
| 138            |

**3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.**
```sql
CREATE TABLE runner_ratings (
   "order_id" INTEGER,
   "customer_id" INTEGER,
   "runner_id" INTEGER,
   "rating" INTEGER
);

INSERT INTO pizza_runner.runner_ratings (order_id, customer_id, runner_id, rating) VALUES
(1, 101, 1, 4),
(2, 101, 1, 3),
(3, 102, 1, 5),
(4, 103, 2, 2),
(5, 104, 3, 4),
(7, 105, 2, 5),
(8, 102, 2, 3),
(10, 104, 1, 5);
```
**Result:**
| order_id | customer_id | runner_id | rating |
| -------- | ----------- | --------- | ------ |
| 1        | 101         | 1         | 4      |
| 2        | 101         | 1         | 3      |
| 3        | 102         | 1         | 5      |
| 4        | 103         | 2         | 2      |
| 5        | 104         | 3         | 4      |
| 7        | 105         | 2         | 5      |
| 8        | 102         | 2         | 3      |
| 10       | 104         | 1         | 5      |

**4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?**
- customer_id
- order_id
- runner_id
- rating
- order_time
- pickup_time
- Time between order and pickup
- Delivery duration
- Average speed
- Total number of pizzas
```sql
CREATE TABLE DELIVERY_INFO (
  "customer_id" INTEGER,
  "order_id" INTEGER,
  "runner_id" INTEGER,
  "rating" INTEGER,
  "order_time" TIMESTAMP,
  "pickup_time" TIMESTAMP,
  "time_between_order_and_pickup" INTEGER,
  "duration" INTEGER,
  "average_speed" FLOAT,
  "total_pizzas" INTEGER
);

INSERT INTO DELIVERY_INFO (customer_id, order_id, runner_id, rating, order_time, pickup_time, time_between_order_and_pickup, duration, average_speed, total_pizzas)
SELECT
  r.customer_id,
  r.order_id, 
  r.runner_id, 
  r.rating,
  co.order_time,
  ro.pickup_time,
  EXTRACT(MINUTE FROM age(ro.pickup_time::timestamp, co.order_time)), 
  ro.duration,
  ROUND((distance * 60)::NUMERIC/duration, 2),
  COUNT(*) 
FROM runner_ratings r JOIN temp_customer_orders co
ON r.order_id = co.order_id
JOIN temp_runner_orders ro
ON r.order_id = ro.order_id
GROUP BY
  r.customer_id,
  r.order_id,
  r.runner_id,
  r.rating,
  co.order_time, 
  ro.pickup_time,
  ro.duration,
  distance
ORDER BY 
  r.customer_id,
  r.order_id, 
  r.runner_id;
```
**Result:**
| customer_id | order_id | runner_id | rating | order_time          | pickup_time         | time_between_order_and_pickup | duration | average_speed | total_pizzas |
| ----------- | -------- | --------- | ------ | ------------------- | ------------------- | ----------------------------- | -------- | ------------- | ------------ |
| 101         | 1        | 1         | 4      | 2020-01-01 18:05:02 | 2020-01-01 18:15:34 | 10                            | 32       | 37.5          | 1            |
| 101         | 2        | 1         | 3      | 2020-01-01 19:00:52 | 2020-01-01 19:10:54 | 10                            | 27       | 44.44         | 1            |
| 102         | 3        | 1         | 5      | 2020-01-02 23:51:23 | 2020-01-03 00:12:37 | 21                            | 20       | 40.2          | 2            |
| 102         | 8        | 2         | 3      | 2020-01-09 23:54:33 | 2020-01-10 00:15:02 | 20                            | 15       | 93.6          | 1            |
| 103         | 4        | 2         | 2      | 2020-01-04 13:23:46 | 2020-01-04 13:53:03 | 29                            | 40       | 35.1          | 3            |
| 104         | 5        | 3         | 4      | 2020-01-08 21:00:29 | 2020-01-08 21:10:57 | 10                            | 15       | 40            | 1            |
| 104         | 10       | 1         | 5      | 2020-01-11 18:34:49 | 2020-01-11 18:50:20 | 15                            | 10       | 60            | 2            |
| 105         | 7        | 2         | 5      | 2020-01-08 21:20:29 | 2020-01-08 21:30:45 | 10                            | 25       | 60            | 1            |

**5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?**
```sql
SELECT
  ROUND(
    ( SUM(CASE WHEN pn.pizza_name = 'Meatlovers' THEN 12 ELSE 10 END) - 
      SUM(ro.distance * 0.3))::NUMERIC
    , 2) AS Pizza_Runner_profit 
 FROM temp_customer_orders co JOIN temp_runner_orders ro
 ON co.order_id = ro.order_id
 JOIN pizza_names pn
 ON pn.pizza_id = co.pizza_id
 WHERE ro.cancellation IS NULL;
```
**Result:**
| pizza_runner_profit |
| ------------------- |
| 73.38               |
