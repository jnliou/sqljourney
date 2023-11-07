## Confirmation Rate - Joins - Medium

November 7, 2023

[LeetCode Question](https://leetcode.com/problems/confirmation-rate/?envType=study-plan-v2&envId=top-sql-50)

![cat](https://media4.giphy.com/media/B278fjCLIP6CcwvHPV/giphy.gif?cid=82a1493bc8imxnyd16qjjt9j5wb9x1btn16e7z2tidi577vs&ep=v1_gifs_search&rid=giphy.gif&ct=s "cat")

## Table Structure

### Signups

- **user_id (int)**: Unique identifier for users. 
- **time_stamp (datetime)**: The date and time when the user signed up.

This table contains information about when a user signed up.

### Confirmations

- **user_id (int)**: Identifier for users, linked to the Signups table.
- **time_stamp (datetime)**: The date and time when the user requested confirmation.
- **action (ENUM)**: Status of the confirmation ('confirmed', 'timeout').

This table records whether a user's request for confirmation was successful or timed out.

## Objective

The confirmation rate of a user is the number of 'confirmed' messages divided by the total number of requested confirmation messages. The confirmation rate of a user that did not request any confirmation messages is 0. Round the confirmation rate to two decimal places.

Write a solution to find the confirmation rate of each user.

Return the result table in any order.

## Result Format

The result will return a table with `user_id` and `confirmation_rate` columns, rounded to two decimal places. 

## Example

Given the Signups and Confirmations tables as input:

### Signups Table

| user_id | time_stamp          |
| ------- | ------------------- |
| 3       | 2020-03-21 10:16:13 |
| 7       | 2020-01-04 13:57:59 |
| 2       | 2020-07-29 23:09:44 |
| 6       | 2020-12-09 10:39:37 |

### Confirmations Table

| user_id | time_stamp          | action    |
| ------- | ------------------- | --------- |
| 3       | 2021-01-06 03:30:46 | timeout   |
| 3       | 2021-07-14 14:00:00 | timeout   |
| 7       | 2021-06-12 11:57:29 | confirmed |
| 7       | 2021-06-13 12:58:28 | confirmed |
| 7       | 2021-06-14 13:59:27 | confirmed |
| 2       | 2021-01-22 00:00:00 | confirmed |.

### Output

| user_id | confirmation_rate |
| ------- | ----------------- |
| 3       | 0                 |
| 7       | 1                 |
| 2       | 0.5               |
| 6       | 0                 |

## Explanation

- User 6 has a confirmation rate of 0.00 as they did not request any confirmation messages.
- User 3 requested 2 confirmations and both timed out, resulting in a rate of 0.
- User 7 had all 3 requests confirmed, achieving a rate of 1.
- User 2's rate is 0.5, with one out of two requests confirmed.

## SQL Query

To calculate the confirmation rate for each user, an SQL query will be used that joins both tables on the `user_id`, counts the number of 'confirmed' actions, and divides by the total number of confirmation requests for each user. The result will be grouped by `user_id` and will use aggregate functions along with the necessary ROUND function to format the confirmation rate.

## SOLUTION:

### Step 1 - Joining the Table

```sql 
SELECT * 
FROM Signups s
LEFT JOIN Confirmations c ON  s.user_id = c.user_id
```

| user_id | time_stamp          | user_id | time_stamp          | action    |
| ------- | ------------------- | ------- | ------------------- | --------- |
| 3       | 2020-03-21 10:16:13 | 3       | 2021-07-14 14:00:00 | timeout   |
| 3       | 2020-03-21 10:16:13 | 3       | 2021-01-06 03:30:46 | timeout   |
| 7       | 2020-01-04 13:57:59 | 7       | 2021-06-14 13:59:27 | confirmed |
| 7       | 2020-01-04 13:57:59 | 7       | 2021-06-13 12:58:28 | confirmed |
| 7       | 2020-01-04 13:57:59 | 7       | 2021-06-12 11:57:29 | confirmed |
| 2       | 2020-07-29 23:09:44 | 2       | 2021-02-28 23:59:59 | timeout   |
| 2       | 2020-07-29 23:09:44 | 2       | 2021-01-22 00:00:00 | confirmed |
| 6       | 2020-12-09 10:39:37 | null    | null                | null      |

* Join the Signup and Confirmation Table Together 
* We can see that the Confirmation.user_id table did not have user_id *6*, so when we want to query for specific column names, we should include *s.user_id*, as it covers all user_ids in the answer (3,7,2,6).



## Step 2 - Querying for confirmation_rate 

```sql
SELECT s.user_id, 
ROUND(IFNULL(AVG(c.action = "confirmed"), 0),2) as confirmation_rate
FROM Signups s
LEFT JOIN Confirmations c ON s.user_id = c.user_id
GROUP BY s.user_id
```

| user_id | confirmation_rate |
| ------- | ----------------- |
| 3       | 0                 |
| 7       | 1                 |
| 2       | 0.5               |
| 6       | 0                 |

* Choose c.action as we have to query the *confirmation* table as it includes the *timeout* and *confirmation* status. 
* Choose s.user_id as it includes all user ids we are assessing. 
* AVG(c.action = "confirmed") takes the average of the confirmed actions 
* IFNULL(column, 0) ensures that the null values are considered 0 in the calculation
* ROUND(,2) ensures that the values are rounded to 2 decimal places

## Reflection on WRONG ANSWER 

*** Always make sure you read the question, initially I submitted the answer as 

```sql SELECT s.user_id, 
IFNULL(AVG(c.action = "confirmed"), 0) as confirmation_rate
FROM Signups s
LEFT JOIN Confirmations c ON s.user_id = c.user_id
GROUP BY s.user_id 
```

and it was considered incorrect as I forgot to round the values to 2 decimal places, although the answer itself did round the values to 2 decimal places. 

* IFNULL appears to be a commonly used function when it comes to assuming that NULL = 0. 