## Case Study Questions

**1. What is the total amount each customer spent at the restaurant?**
```sql
SELECT 	
    customer_id, 
    SUM(price) AS total_spend
FROM dannys_diner.sales JOIN dannys_diner.menu 
ON sales.product_id = menu.product_id
GROUP BY customer_id
ORDER BY customer_id; 
```
**Result:**
| customer_id | total_spend |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

**2. How many days has each customer visited the restaurant?**
```sql
SELECT 
    customer_id,
    COUNT(DISTINCT order_date) AS total_visits
FROM dannys_diner.sales
GROUP BY customer_id;
```
**Result:**
| customer_id | total_visits |
| ----------- | -------------|
| A           | 4            |
| B           | 6            |
| C           | 2            |

**3. What was the first item from the menu purchased by each customer?**
```sql
WITH sales_order AS (
SELECT 
    sales.customer_id,
    menu.product_name,
    DENSE_RANK() OVER(
    	PARTITION BY sales.customer_id
      	ORDER BY sales.order_date) AS rank
FROM dannys_diner.sales JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
)
SELECT 
    customer_id,
    product_name AS first_purchase
FROM sales_order 
WHERE rank = 1
GROUP BY customer_id, product_name;
```
**Result:**
| customer_id | first_purchase |
| ----------- | -------------- |
| A           | curry          |
| A           | sushi          |
| B           | curry          |
| C           | ramen          |

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
```sql
SELECT
    product_name AS most_purchased_item,
    COUNT(*) AS purchase_count
FROM dannys_diner.sales JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY product_name
ORDER BY purchase_count DESC
LIMIT 1;
```
**Result:**
| most_purchased_item | purchase_count |
| ------------------- | -------------- |
| ramen               | 8              |

**5. Which item was the most popular for each customer?**
```sql
WITH item_order AS (
  SELECT 
  	customer_id,
  	product_name,
  	COUNT(menu.product_id) AS order_count,
  	DENSE_RANK() OVER(
	      PARTITION BY customer_id
	      ORDER BY COUNT(product_name) DESC) AS rank
  FROM dannys_diner.sales JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
  GROUP BY customer_id, product_name
)
SELECT 
    customer_id,	
    product_name,
    order_count
FROM item_order 
WHERE rank = 1;
```
**Result:**
| customer_id | product_name | order_count |
| ----------- | ------------ | ----------- |
| A           | ramen        | 3           |
| B           | ramen        | 2           |
| B           | curry        | 2           |
| B           | sushi        | 2           |
| C           | ramen        | 3           |

**6. Which item was purchased first by the customer after they became a member?**
```sql
WITH purchase_order AS
(
  SELECT 
    sales.customer_id,
    product_id,
    DENSE_RANK() OVER (
      PARTITION BY sales.customer_id
      ORDER BY sales.order_date) AS rank
  FROM dannys_diner.sales JOIN dannys_diner.members
  ON sales.customer_id = members.customer_id
  WHERE sales.order_date > members.join_date
)
SELECT 
    customer_id,
    product_name AS first_purchase_after_membership
FROM purchase_order JOIN dannys_diner.menu
ON purchase_order.product_id = menu.product_id
WHERE rank = 1
ORDER BY customer_id;
```
**Result:**
| customer_id | first_purchase_after_membership |
| ----------- | ------------------------------- |
| A           | ramen          			|
| B           | sushi          			|

**7. Which item was purchased just before the customer became a member?**
```sql
WITH purchase_order AS
(
  SELECT 
	sales.customer_id,
    sales.product_id,
    DENSE_RANK() OVER (
      PARTITION BY sales.customer_id
      ORDER BY sales.order_date DESC) AS rank
  FROM dannys_diner.sales JOIN dannys_diner.members
  ON sales.customer_id = members.customer_id
  WHERE sales.order_date < members.join_date
)
SELECT 
	purchase_order.customer_id,
    menu.product_name AS purchase_before_membership
FROM purchase_order JOIN dannys_diner.menu
ON purchase_order.product_id = menu.product_id
WHERE rank = 1
ORDER BY customer_id;
```
**Result:**
| customer_id | purchase_before_membership |
| ----------- | -------------------------- |
| A           | sushi                      |
| A           | curry                      |
| B           | sushi                      |

**8. What is the total items and amount spent for each member before they became a member?**
```sql
SELECT
    sales.customer_id,
    COUNT(*) AS total_items_before_membership,
    SUM(menu.price) AS total_amount_before_membership
FROM dannys_diner.sales JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
JOIN dannys_diner.members
ON members.customer_id = sales.customer_id
WHERE order_date < join_date
GROUP BY sales.customer_id
ORDER BY sales.customer_id;
```
**Result:**
| customer_id | total_items_before_membership | total_amount_before_membership |
| ----------- | ----------------------------- | ------------------------------ |
| A           | 2                             | 25                             |
| B           | 3                             | 40                             |

**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**
```sql
WITH cte_points AS (
  SELECT 
  	product_id,
  	CASE WHEN product_name = 'sushi' THEN 2*price*10
  	ELSE price*10 END AS points
  FROM dannys_diner.menu
 )
 SELECT 
    customer_id,
    SUM(points) AS total_points
 FROM cte_points JOIN dannys_diner.sales
 ON cte_points.product_id = sales.product_id
 GROUP BY customer_id
 ORDER BY customer_id;
```
**Result:**
| customer_id | total_points |
| ----------- | ------------ |
| A           | 860          |
| B           | 940          |
| C           | 360          |

**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**
```sql
```
**Result:**
