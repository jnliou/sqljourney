### Week2 - Percentage of Users Attended a Contest
November 16, 2023

[LeetCode Question](https://leetcode.com/problems/percentage-of-users-attended-a-contest/submissions/1100374027/?envType=study-plan-v2&envId=top-sql-50)
![Contest](https://www.icegif.com/wp-content/uploads/winner-icegif-9.gif)

#### Background
We have two tables: `Users` and `Register`. `Users` table contains user details, and `Register` table contains the records of users registering for various contests.

#### Table: Users

| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| user_name   | varchar |

- `user_id` is the primary key for this table.
- Each row contains the name and the id of a user.

#### Table: Register

| Column Name | Type |
|-------------|------|
| contest_id  | int  |
| user_id     | int  |

- `(contest_id, user_id)` is the primary key for this table.
- Each row contains the id of a user and the contest they registered into.

#### Objective
Write a solution to find the percentage of users registered in each contest, rounded to two decimals.

#### Requirements
- Return the result table ordered by percentage in descending order.
- In case of a tie, order by `contest_id` in ascending order.

#### Example

**Input:** 

*Users table:*

| user_id | user_name |
|---------|-----------|
| 6       | Alice     |
| 2       | Bob       |
| 7       | Alex      |

*Register table:*

| contest_id | user_id |
|------------|---------|
| 215        | 6       |
| 209        | 2       |
| ...        | ...     |

**Output:** 

| contest_id | percentage |
|------------|------------|
| 208        | 100.0      |
| 209        | 100.0      |
| ...        | ...        |

**Explanation:** 
- All users registered in contests 208, 209, and 210, making the percentage 100%.
- Alice and Alex registered in contest 215, resulting in a percentage of 66.67%.
- Bob was the only one who registered in contest 207, leading to a percentage of 33.33%.

### LEFT JOIN

```sql
SELECT * 
FROM Users u
LEFT JOIN Register r ON r.user_id = u.user_id
```

| user_id | user_name | contest_id | user_id |
| ------- | --------- | ---------- | ------- |
| 6       | Alice     | 209        | 6       |
| 6       | Alice     | 208        | 6       |
| 6       | Alice     | 210        | 6       |
| 6       | Alice     | 215        | 6       |
| 2       | Bob       | 207        | 2       |
| 2       | Bob       | 210        | 2       |
| 2       | Bob       | 208        | 2       |
| 2       | Bob       | 209        | 2       |
| 7       | Alex      | 210        | 7       |
| 7       | Alex      | 208        | 7       |
| 7       | Alex      | 215        | 7       |
| 7       | Alex      | 209        | 7       |

### INNER JOIN
```sql 
SELECT * 
FROM Users u
JOIN Register r ON r.user_id = u.user_id
```

| user_id | user_name | contest_id | user_id |
| ------- | --------- | ---------- | ------- |
| 6       | Alice     | 209        | 6       |
| 6       | Alice     | 208        | 6       |
| 6       | Alice     | 210        | 6       |
| 6       | Alice     | 215        | 6       |
| 2       | Bob       | 207        | 2       |
| 2       | Bob       | 210        | 2       |
| 2       | Bob       | 208        | 2       |
| 2       | Bob       | 209        | 2       |
| 7       | Alex      | 210        | 7       |
| 7       | Alex      | 208        | 7       |
| 7       | Alex      | 215        | 7       |
| 7       | Alex      | 209        | 7       |

```sql SELECT *
FROM Users u
JOIN Register r ON r.user_id = u.user_id
ORDER BY contest_id
```

| user_id | user_name | contest_id | user_id |
| ------- | --------- | ---------- | ------- |
| 2       | Bob       | 207        | 2       |
| 2       | Bob       | 208        | 2       |
| 6       | Alice     | 208        | 6       |
| 7       | Alex      | 208        | 7       |
| 2       | Bob       | 209        | 2       |
| 7       | Alex      | 209        | 7       |
| 6       | Alice     | 209        | 6       |
| 6       | Alice     | 210        | 6       |
| 2       | Bob       | 210        | 2       |
| 7       | Alex      | 210        | 7       |
| 6       | Alice     | 215        | 6       |
| 7       | Alex      | 215        | 7       |

## NO NEED FOR A JOIN -  d/t excess processing

```sql
SELECT contest_id, ROUND((COUNT(user_id)/(select COUNT(DISTINCT user_id) from users))*100,2) As percentage
FROM Register
GROUP BY contest_id
ORDER BY percentage DESC, contest_id ASC 
```

Both were accepted. The bottom one beats 99.65% of users, but it's good to remember the concept of *distinct* values when querying data. 

```sql
SELECT contest_id, ROUND((COUNT(user_id)/(select COUNT(user_id) from users))*100,2) As percentage
FROM Register
GROUP BY contest_id
ORDER BY percentage DESC, contest_id ASC 
```
**Sub-query**
* A sub-query was needed to query this data. 

* We GROUP BY contest_id to ensure that we were only querying columns based on the contest_id row 
* To obtain the percentage column we needed to *ROUND* the values by 2, hence ROUND(,2)
* Then we *COUNT* the users in user_id FROM register
* To differentiate the *user_id* from registered and users tables, we needed to create a subquery to sort out the DISTINCT user_id and perform a COUNT(user_id) from users table. This would ensure that the values would be dynamic.
* We needed to multiple the equation by 100 to obtaint he percentage

## Reflection of *sub-query*

Subqueries, also known as nested queries or inner queries, are SQL queries used within another SQL query. They are quite powerful and useful in various scenarios. Here are some common situations where subqueries can be particularly beneficial:

### 1. Filtering Results
When you need to filter data based on a condition that requires calculation or aggregation. For instance, finding all employees who earn more than the average salary in their department. The average salary per department is calculated in the subquery.

### 2. Data Aggregation and Analysis
For complex data analysis that involves multiple steps of data processing. For example, finding the maximum sales of each product and then using this information to filter out products that have reached a certain threshold.

### 3. Column Generation
To create a new column in a SELECT statement where the value depends on data from the same or another table. For instance, a column indicating whether a customer's total purchases exceed a certain amount, where this total amount is calculated in a subquery.

### 4. Join Replacement
As an alternative to JOINs, especially when working with EXISTS, IN, or NOT IN clauses. This can be particularly useful when the relationship between tables is complex or when a JOIN operation would be too costly in terms of performance.

### 5. Data Validation
To check the existence or absence of records in another table. For instance, you might use a subquery to find all customers who have not made any purchases.

### 6. Simplifying Complex Queries
To break down complex queries into manageable parts, making the query easier to understand and maintain. It helps in structuring the query in a step-by-step manner.

### 7. Correlated Subqueries
When you need to reference a column from the outer query within the subquery, often used in UPDATE and DELETE statements. For example, updating the price in a products table based on a condition that involves data in an orders table.

### Things to Keep in Mind:
- **Performance**: Subqueries can sometimes lead to poor performance, especially if they are not well optimized or if the database engine does not handle them efficiently.
- **Readability**: Overusing subqueries can make your SQL code less readable. It's essential to strike a balance between functionality and clarity.
- **Alternatives**: Always consider if a JOIN or a window function might be a more efficient choice for the task at hand.

Understanding when and how to effectively use subqueries can significantly enhance your ability to write powerful and efficient SQL queries.
