
## Queries Quality and Percentage
November 17, 2023
[LeetCode Question](https://leetcode.com/problems/queries-quality-and-percentage/description/?envType=study-plan-v2&envId=top-sql-50)
![DogCat](https://media1.popsugar-assets.com/files/thumbor/HNTGuST7AK8Lt6UbQq5YA3usrG8=/fit-in/500x214/top/filters:format_auto():upscale()/2014/01/14/951/n/1922243/2a11e9a00e0c1465_tumblr_mhc5ukFAdr1qf4k86o1_500.gif)

### Table: Queries

| Column Name | Type    |
|-------------|---------|
| query_name  | varchar |
| result      | varchar |
| position    | int     |
| rating      | int     |

- This table may have duplicate rows.
- The table contains information collected from some queries on a database.
- The `position` column has values from 1 to 500.
- The `rating` column has values from 1 to 5. A query with a rating less than 3 is considered a poor query.

### Problem Statement

We define **query quality** as the average of the ratio between query rating and its position.

We also define **poor query percentage** as the percentage of all queries with a rating less than 3.

**Objective:** Write a solution to find each `query_name`, the `quality`, and `poor_query_percentage`. Both `quality` and `poor_query_percentage` should be rounded to 2 decimal places. Return the result table in any order.

### Example

#### Input

Queries table:

| query_name | result            | position | rating |
|------------|-------------------|----------|--------|
| Dog        | Golden Retriever  | 1        | 5      |
| Dog        | German Shepherd   | 2        | 5      |
| Dog        | Mule              | 200      | 1      |
| Cat        | Shirazi           | 5        | 2      |
| Cat        | Siamese           | 3        | 3      |
| Cat        | Sphynx            | 7        | 4      |

#### Output

| query_name | quality | poor_query_percentage |
|------------|---------|-----------------------|
| Dog        | 2.50    | 33.33                 |
| Cat        | 0.66    | 33.33                 |

#### Explanation

- Dog queries quality is ((5 / 1) + (5 / 2) + (1 / 200)) / 3 = 2.50
- Dog queries poor query percentage is (1 / 3) * 100 = 33.33
- Cat queries quality equals ((2 / 5) + (3 / 3) + (4 / 7)) / 3 = 0.66
- Cat queries poor query percentage is (1 / 3) * 100 = 33.33

### Solution

#### Query for quality 
```sql
SELECT AVG(rating/position)
FROM Queries
GROUP BY query_name
```

| AVG(rating/position) |
| -------------------- |
| 2.50166667           |
| 0.65713333           |

#### Adding the querry_name column, and rounding the query to 2 decimal places 

```sql 
SELECT query_name, ROUND(AVG(rating/position),2) AS quality
FROM Queries
GROUP BY query_name

```


| query_name | quality |
| ---------- | ------- |
| Dog        | 2.5     |
| Cat        | 0.66    |

#### Query for poor_query_percentage

##### 1 - Create a CASE statement to separate those with "poor ratings" as 1, versus not poor ratings as 0. 
```sql 
SELECT query_name, 
CASE 
WHEN rating < 3 
THEN 1 
ELSE 0 END AS poor_query_percentage
FROM Queries
```

| query_name | poor_query_percentage |
| ---------- | --------------------- |
| Dog        | 0                     |
| Dog        | 0                     |
| Dog        | 1                     |
| Cat        | 1                     |
| Cat        | 0                     |
| Cat        | 0                     |

##### 2 Determine how many poor ratings there are within each query_name

```sql
SELECT query_name, ROUND(AVG(rating/position),2) AS quality,
SUM(CASE 
WHEN rating < 3 
THEN 1 
ELSE 0 END)
AS poor_query_percentage
FROM Queries
GROUP BY query_name
```

| query_name | quality | poor_query_percentage |
| ---------- | ------- | --------------------- |
| Dog        | 2.5     | 1                     |
| Cat        | 0.66    | 1                     |


** We need to *SUM* the CASE Statement to determine how many rows fit that CASE statement. 

##### 3 Create the percentage equation 

```sql
SELECT query_name, ROUND(AVG(rating/position),2) AS quality,
ROUND(SUM(CASE WHEN rating < 3 THEN 1 ELSE 0 END)/COUNT(rating)*100,2) AS poor_query_percentage
FROM Queries
GROUP BY query_name
```
* Created the percentage equation by dividing the SUM(CASE) by the COUNT of ratings (total number of ratings per each query_name) * 100 
* ROUND to ensure it's rounded to 2 decimal places 

| query_name | quality | poor_query_percentage |
|------------|---------|-----------------------|
| Dog        | 2.50    | 33.33                 |
| Cat        | 0.66    | 33.33                 |

### Reflection
* COUNT would just count the number of rows, but SUM would count the values in the rows, even within a CASE Statement. 

#### Understanding *Aggregate* 

Aggregation in the context of SQL refers to the process of combining multiple rows of data into a single row to perform calculations or summarize information. This is commonly achieved using aggregate functions, which are special functions in SQL designed to perform calculations on a set of values and return a single value.

Aggregate Functions: Common aggregate functions include:

SUM(): Calculates the total sum of a numeric column.
AVG(): Computes the average of a numeric column.
COUNT(): Counts the number of rows that match a specified condition.
MIN() and MAX(): Find the minimum and maximum values in a column, respectively.
GROUP BY Clause: This clause is used in conjunction with aggregate functions to group rows that have the same values in specified columns. It allows you to perform aggregate calculations on each group separately.

HAVING Clause: Similar to the WHERE clause, but used for filtering data after an aggregation has been performed. The WHERE clause filters rows before aggregation, whereas the HAVING clause filters groups after aggregation.

Distinct Values: Aggregate functions can be used with the DISTINCT keyword to perform calculations on unique values only.

Combining with Other SQL Features: Aggregation can be combined with other SQL clauses and features like JOINs, subqueries, and ORDER BY for more complex data manipulations.