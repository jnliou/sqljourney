Week2-Monthly-Transactions-I

November 18, 2023

[LeetcodeQuestion](https://leetcode.com/problems/monthly-transactions-i/description/?envType=study-plan-v2&envId=top-sql-50)
![payday](https://media.tenor.com/CHZgLd_zRIoAAAAi/blue-cat.gif)

## Table: Transactions

The `Transactions` table stores information about incoming transactions. Below is the structure of the table:

| Column Name | Type    | Description                                        |
|-------------|---------|----------------------------------------------------|
| id          | int     | Primary key.                                       |
| country     | varchar | Country where the transaction occurred.            |
| state       | enum    | The state of the transaction (approved/declined).  |
| amount      | int     | The amount of the transaction.                     |
| trans_date  | date    | The date when the transaction occurred.            |

**Note:** 
- The `state` column is an enum of type `["approved", "declined"]`.

### Objective

Write an SQL query to find for each month and country:
- The number of transactions (`trans_count`) and their total amount (`trans_total_amount`).
- The number of approved transactions (`approved_count`) and their total amount (`approved_total_amount`).

The result should be returned in any order.

### Example

#### Input

**Transactions table:**

| id  | country | state    | amount | trans_date |
|-----|---------|----------|--------|------------|
| 121 | US      | approved | 1000   | 2018-12-18 |
| 122 | US      | declined | 2000   | 2018-12-19 |
| 123 | US      | approved | 2000   | 2019-01-01 |
| 124 | DE      | approved | 2000   | 2019-01-07 |

#### Output

**Result table:**

| month   | country | trans_count | approved_count | trans_total_amount | approved_total_amount |
|---------|---------|-------------|----------------|--------------------|-----------------------|
| 2018-12 | US      | 2           | 1              | 3000               | 1000                  |
| 2019-01 | US      | 1           | 1              | 2000               | 2000                  |
| 2019-01 | DE      | 1           | 1              | 2000               | 2000                  |

### Extract for approved_count

```sql
SELECT country,
SUM(CASE
WHEN state = "approved" THEN 1
ELSE 0
END) AS approved_count
FROM Transactions 
GROUP BY country
```

| country | approved_count |
| ------- | -------------- |
| US      | 2              |
| DE      | 1              |

* Wanting to determine the count of the transactions, GROUP BY Country 

### Adding the Transaction Counts

```sql
SELECT country, COUNT(trans_date) AS trans_count,
SUM(CASE
WHEN state = "approved" THEN 1
ELSE 0
END) AS approved_count
FROM Transactions 
GROUP BY country
```

| country | trans_count | approved_count |
| ------- | ----------- | -------------- |
| US      | 3           | 2              |
| DE      | 1           | 1              |

### Extract for Trans Total amount

```sql 
SELECT country, COUNT(trans_date) AS trans_count,
SUM(CASE
WHEN state = "approved" THEN 1
ELSE 0
END) AS approved_count, SUM(amount) AS trans_total_amount
FROM Transactions 
GROUP BY country
```

| country | trans_count | approved_count | trans_total_amount |
| ------- | ----------- | -------------- | ------------------ |
| US      | 3           | 2              | 5000               |
| DE      | 1           | 1              | 2000               |

### Extract by approved_total_amount

``` sql 
SELECT country, COUNT(trans_date) AS trans_count,
SUM(CASE
WHEN state = "approved" THEN 1
ELSE 0
END) AS approved_count, SUM(amount) AS trans_total_amount, 
SUM(
  CASE
    WHEN state = 'approved' THEN amount
    ELSE 0
  END
) AS approved_total_amount
FROM Transactions 
GROUP BY country

```

| country | trans_count | approved_count | trans_total_amount | approved_total_amount |
| ------- | ----------- | -------------- | ------------------ | --------------------- |
| US      | 3           | 2              | 5000               | 3000                  |
| DE      | 1           | 1              | 2000               | 2000      

* OR we can use an IF STATEMENT

```sql
 SELECT country, COUNT(trans_date) AS trans_count,
SUM(CASE
WHEN state = "approved" THEN 1
ELSE 0
END) AS approved_count, SUM(amount) AS trans_total_amount, 
SUM(
  IF(state = "approved", amount, 0)) AS approved_total_amount
FROM Transactions 
GROUP BY country
```

```sql 
SUM(
  IF(state = "approved", amount, 0)) AS approved_total_amount
  ```

  ### Extract for months 


  | month | country | trans_count | approved_count | trans_total_amount | approved_total_amount |
| ----- | ------- | ----------- | -------------- | ------------------ | --------------------- |
| 12    | US      | 2           | 1              | 3000               | 1000                  |
| 1     | US      | 1           | 1              | 2000               | 2000                  |
| 1     | DE      | 1           | 1              | 2000               | 2000                  |


```sql
# Write your MySQL query statement below
SELECT DATE_FORMAT(trans_date, '%Y-%m') AS month, country, COUNT(trans_date) AS trans_count,
SUM(CASE
WHEN state = "approved" THEN 1
ELSE 0
END) AS approved_count, SUM(amount) AS trans_total_amount, 
SUM(
  IF(state = "approved", amount, 0)) AS approved_total_amount
FROM Transactions 
GROUP BY month, country

```

| month   | country | trans_count | approved_count | trans_total_amount | approved_total_amount |
|---------|---------|-------------|----------------|--------------------|-----------------------|
| 2018-12 | US      | 2           | 1              | 3000               | 1000                  |
| 2019-01 | US      | 1           | 1              | 2000               | 2000                  |
| 2019-01 | DE      | 1           | 1              | 2000               | 2000                  |

#### DATE FORMAT EQUATION

*  DATE_FORMAT(trans_date, '%Y-%m') - used to extract the date and month of a column 
