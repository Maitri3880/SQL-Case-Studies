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
```
**Result:**

**3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name**
```sql
```
**Result:**

**4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?**
```sql
SELECT
  COUNT(DISTINCT(s.customer_id)) AS churn_customer_count,
    (
      COUNT(DISTINCT(s.customer_id))::FLOAT / 
      (SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions)
    ) * 100.0 AS churn_customer_pct
FROM foodie_fi.subscriptions s JOIN foodie_fi.plans p
ON s.plan_id = p.plan_id
WHERE p.plan_name = 'churn';
```
**Result:**
| churn_customer_count | churn_customer_pct |
| -------------------- | ------------------ |
| 307                  | 30.7               |

**5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?**
```sql
```
**Result:**

**6. What is the number and percentage of customer plans after their initial free trial?**
```sql
```
**Result:**

**7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?**
```sql
```
**Result:**

**8. How many customers have upgraded to an annual plan in 2020?**
```sql
```
**Result:**

**9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?**
```sql
```
**Result:**

**10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)**
```sql
```
**Result:**

**11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?**
```sql
```
**Result:**
