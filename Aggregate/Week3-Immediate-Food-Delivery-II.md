# Week3
November 19, 2023
[Leetcode Question](https://leetcode.com/problems/immediate-food-delivery-ii/?envType=study-plan-v2&envId=top-sql-50)
![foodorder](https://media.tenor.com/vgWNUVgKiMUAAAAi/cute-adorable.gif)

Input:
| delivery_id | customer_id | order_date | customer_pref_delivery_date |
| ----------- | ----------- | ---------- | --------------------------- |
| 1           | 1           | 2019-08-01 | 2019-08-02                  |
| 2           | 2           | 2019-08-02 | 2019-08-02                  |
| 3           | 1           | 2019-08-11 | 2019-08-12                  |
| 4           | 3           | 2019-08-24 | 2019-08-24                  |
| 5           | 3           | 2019-08-21 | 2019-08-22                  |
| 6           | 2           | 2019-08-11 | 2019-08-13                  |
| 7           | 4           | 2019-08-09 | 2019-08-09                  |

If the customer's preferred delivery date is the same as the order date, then the order is called immediate; otherwise, it is called scheduled.

The first order of a customer is the order with the earliest order date that the customer made. It is guaranteed that a customer has precisely one first order.

Write a solution to find the percentage of immediate orders in the first orders of all customers, rounded to 2 decimal places.

### Solution

#### Querying to determine the immediate orders 

```sql
SELECT customer_id, CASE
WHEN order_date = customer_pref_delivery_date THEN 1
ELSE 0
END AS immediate
FROM Delivery 
```

| customer_id | immediate |
| ----------- | --------- |
| 1           | 0         |
| 2           | 1         |
| 1           | 0         |
| 3           | 1         |
| 3           | 0         |
| 2           | 0         |
| 4           | 1         |

#### Querying for first orders using *sub queries* 

```sql
SELECT *
FROM Delivery 
WHERE (customer_id, order_date) IN
(
SELECT 
    customer_id, 
    MIN(order_date) AS FirstOrderDate
FROM 
    Delivery
GROUP BY customer_id)
```

| delivery_id | customer_id | order_date | customer_pref_delivery_date |
| ----------- | ----------- | ---------- | --------------------------- |
| 1           | 1           | 2019-08-01 | 2019-08-02                  |
| 2           | 2           | 2019-08-02 | 2019-08-02                  |
| 5           | 3           | 2019-08-21 | 2019-08-22                  |
| 7           | 4           | 2019-08-09 | 2019-08-09                  |

#### Adding the CASE statement along with our subquery 

```sql
SELECT 
CASE
WHEN order_date = customer_pref_delivery_date 
THEN 1
ELSE 0
END AS Immediate 
FROM Delivery 
WHERE (customer_id, order_date) IN
(
SELECT 
    customer_id, 
    MIN(order_date) AS FirstOrderDate
FROM 
    Delivery
GROUP BY customer_id)

```

| Immediate |
| --------- |
| 0         |
| 1         |
| 0         |
| 1         |

#### Final query - determining the percentage 

```sql
SELECT 
ROUND(AVG(CASE
WHEN order_date = customer_pref_delivery_date 
THEN 1
ELSE 0
END)*100,2) AS immediate_percentage 
FROM Delivery 
WHERE (customer_id, order_date) IN
(
SELECT 
    customer_id, 
    MIN(order_date) AS FirstOrderDate
FROM 
    Delivery
GROUP BY customer_id)
```

| immediate_percentage |
| -------------------- |
| 50                   |

### Subqery 
* Needing to create a *subquery*, to filter for the first order date 
* REMINDER - WHERE statements can help filter the table, I was trying to extract the data earlier all within the SELECT statement