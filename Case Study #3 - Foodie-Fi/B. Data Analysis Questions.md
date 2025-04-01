# B. Data Analysis Questions

**1. How many customers has Foodie-Fi ever had?**
```sql
SELECT
  COUNT(DISTINCT customer_id) AS num_of_customers
FROM foodie_fi.subscriptions;
```
**Result:**
| num_of_customers |
| ---------------- |
| 1000             |

**2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value**
```sql
SELECT 
  EXTRACT(MONTH FROM s.start_date) AS month,
  COUNT(*) AS no_of_subscribers
FROM foodie_fi.subscriptions s JOIN foodie_fi.plans p
ON s.plan_id = p.plan_id
WHERE p.plan_name = 'trial'
GROUP BY EXTRACT(MONTH FROM s.start_date)
ORDER BY EXTRACT(MONTH FROM s.start_date);
```
**Result:**
| month | no_of_subscribers |
| ----- | ----------------- |
| 1     | 88                |
| 2     | 68                |
| 3     | 94                |
| 4     | 81                |
| 5     | 88                |
| 6     | 79                |
| 7     | 89                |
| 8     | 88                |
| 9     | 87                |
| 10    | 79                |
| 11    | 75                |
| 12    | 84                |

**3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name**
```sql
```
**Result:**

**4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?**
```sql
SELECT
  COUNT(DISTINCT(s.customer_id)) AS churn_customer_count,
  ROUND(
        (
          COUNT(DISTINCT(s.customer_id))::FLOAT / 
          (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions) * 100.0
        )::NUMERIC
       , 1) AS churn_customer_pct
FROM foodie_fi.subscriptions s
JOIN foodie_fi.plans p ON s.plan_id = p.plan_id
WHERE p.plan_name = 'churn';
```
**Result:**
| churn_customer_count | churn_customer_pct |
| -------------------- | ------------------ |
| 307                  | 30.7               |

**5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?**
```sql
WITH cte_rn AS
(
  SELECT
    customer_id,
    plan_name,
    ROW_NUMBER() OVER(
      PARTITION BY customer_id
      ORDER BY start_date) AS rn
  FROM foodie_fi.subscriptions s JOIN foodie_fi.plans p
  ON s.plan_id = p.plan_id
)
SELECT 
  COUNT(*) AS customer_churn_after_trial,
  ROUND(
        (
          COUNT(*)::FLOAT / 
          (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions) * 100.0
        )::NUMERIC
       , 1) AS churn_after_trial_pct
FROM cte_rn
WHERE rn = 2 AND plan_name = 'churn';
```
**Result:**
| customer_churn_after_trial | churn_after_trial_pct |
| -------------------------- | --------------------- |
| 92                         | 9.2                   |

**6. What is the number and percentage of customer plans after their initial free trial?**
```sql
WITH cte_rn AS
(
  SELECT
    customer_id,
    plan_name,
    ROW_NUMBER() OVER(
      PARTITION BY customer_id
      ORDER BY start_date) AS rn
  FROM foodie_fi.subscriptions s JOIN foodie_fi.plans p
  ON s.plan_id = p.plan_id
)
SELECT 
  plan_name,
  COUNT(DISTINCT customer_id) AS no_of_customers,
  ROUND(
        (
          COUNT(DISTINCT(customer_id))::FLOAT / 
          (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions) * 100.0
        )::NUMERIC
       , 1) AS customer_pct
FROM cte_rn
WHERE rn = 2
GROUP BY plan_name;	
```
**Result:**
| plan_name     | no_of_customers | customer_pct |
| ------------- | --------------- | ------------ |
| basic monthly | 546             | 54.6         |
| churn         | 92              | 9.2          |
| pro annual    | 37              | 3.7          |
| pro monthly   | 325             | 32.5         |

**7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?**
```sql
WITH ordered_date AS 
(
  SELECT
    customer_id,
    plan_id,
    ROW_NUMBER() OVER (
        PARTITION BY customer_id
        ORDER BY start_date DESC) AS rn
  FROM foodie_fi.subscriptions 
  WHERE start_date <= '2020-12-31' 
)
SELECT
  p.plan_name,
  COUNT(*) AS no_of_customers,
  ROUND(
        (
          COUNT(*)::FLOAT / 
          (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions) * 100.0
        )::NUMERIC
       , 1) AS customer_pct
FROM ordered_date o JOIN foodie_fi.plans p
ON o.plan_id = p.plan_id
WHERE rn = 1
GROUP BY p.plan_id, p.plan_name
ORDER BY p.plan_id;
```
**Result:**
| plan_name     | no_of_customers | customer_pct |
| ------------- | --------------- | ------------ |
| trial         | 19              | 1.9          |
| basic monthly | 224             | 22.4         |
| pro monthly   | 326             | 32.6         |
| pro annual    | 195             | 19.5         |
| churn         | 236             | 23.6         |

**8. How many customers have upgraded to an annual plan in 2020?**
```sql
SELECT
  COUNT(s.customer_id) AS customers_upgraded_to_annual_plan_in_2020
FROM foodie_fi.subscriptions s JOIN foodie_fi.plans p
ON s.plan_id = p.plan_id
WHERE EXTRACT(YEAR FROM s.start_date) = '2020' AND 
p.plan_name = 'pro annual';
```
**Result:**
| customers_upgraded_to_annual_plan_in_2020 |
| ----------------------------------------- |
| 195                                       |

**9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?**
```sql
WITH trial_cte AS (
  SELECT 
    customer_id, 
    start_date AS trial_date
  FROM foodie_fi.subscriptions
  WHERE plan_id = 0
),
annual_cte AS (
  SELECT 
    customer_id, 
    start_date AS annual_date
  FROM foodie_fi.subscriptions
  WHERE plan_id = 3
)
SELECT 
  ROUND(AVG(a.annual_date - t.trial_date),2) AS avg_days_for_upgrade_to_annual
FROM trial_cte AS t JOIN annual_cte AS a
ON t.customer_id = a.customer_id;
```
**Result:**
| avg_days_for_upgrade_to_annual |
| ------------------------------ |
| 104.62                         |

**10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)**
```sql
```
**Result:**

**11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?**
```sql
WITH ranked_cte AS (
  SELECT
    s.customer_id,
    plans.plan_id,
    LEAD(plans.plan_id) OVER (
      PARTITION BY s.customer_id
      ORDER BY s.start_date
    ) AS next_plan_id
  FROM foodie_fi.subscriptions AS s JOIN foodie_fi.plans
  ON s.plan_id = plans.plan_id
  WHERE EXTRACT(YEAR FROM s.start_date) = '2020'
)
SELECT
  COUNT(customer_id) AS customers_downgrading
FROM ranked_cte
WHERE plan_id = 2 AND next_plan_id = 1; 
```
**Result:**
| customers_downgrading |
| --------------------- |
| 0                     |
