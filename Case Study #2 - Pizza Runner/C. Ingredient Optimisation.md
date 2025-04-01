# C. Ingredient Optimisation

**1. What are the standard ingredients for each pizza?**
```sql
WITH ingredients_cte AS (
  SELECT
    pizza_id,
    unnest(string_to_array(toppings, ', '))::INTEGER AS topping_id
  FROM pizza_recipes
)
SELECT
  pn.pizza_name,
  string_agg(pt.topping_name,', ') AS toppings
FROM ingredients_cte i
JOIN pizza_toppings pt ON i.topping_id = pt.topping_id
JOIN pizza_names pn ON pn.pizza_id = i.pizza_id
GROUP BY pn.pizza_name;
```
**Result:**
| pizza_name | toppings                                                              |
| ---------- | --------------------------------------------------------------------- |
| Meatlovers | BBQ Sauce, Pepperoni, Cheese, Salami, Chicken, Bacon, Mushrooms, Beef |
| Vegetarian | Tomato Sauce, Cheese, Mushrooms, Onions, Peppers, Tomatoes            |

**2. What was the most commonly added extra?**
```sql
WITH extra_cte AS
(
  SELECT
    order_id,
    unnest(string_to_array(extras, ', '))::INTEGER AS topping_id
  FROM temp_customer_orders
)
SELECT
  pt.topping_name AS most_common_extra
FROM extra_cte e JOIN pizza_toppings pt
ON e.topping_id = pt.topping_id
GROUP BY pt.topping_name
ORDER BY COUNT(*) DESC
LIMIT 1;
```
**Result:**
| most_common_extra |
| ----------------- |
| Bacon             |

**3. What was the most common exclusion?**
```sql
WITH exclusions_cte AS
(
  SELECT
    order_id,
    unnest(string_to_array(exclusions, ', '))::INTEGER AS topping_id
  FROM temp_customer_orders
)
SELECT
  pt.topping_name AS most_common_exclusion
FROM exclusions_cte e JOIN pizza_toppings pt
ON e.topping_id = pt.topping_id
GROUP BY pt.topping_name
ORDER BY COUNT(*) DESC
LIMIT 1;
```
**Result:**
| most_common_exclusion |
| --------------------- |
| Cheese                |
