# SQL Query to Calculate Average Experience Years per Project

## Introduction

[LeetCode Question](https://leetcode.com/problems/project-employees-i/description/?envType=study-plan-v2&envId=top-sql-50)
![penguin](https://media1.giphy.com/media/d9mkbc1QkvBnHthaQp/200w.gif?cid=6c09b952iermyyv5n09h8lv9si7n3x5dch3jgrcz34el7qzf&ep=v1_gifs_search&rid=200w.gif&ct=g)

This document describes a SQL query designed to calculate the average experience years of employees working on each project. The database consists of two tables: `Project` and `Employee`.

## Database Schema

### Table 1: Project
- **project_id** (int): The ID of the project.
- **employee_id** (int): The ID of the employee working on the project.
- The combination `(project_id, employee_id)` serves as the primary key.
- `employee_id` is a foreign key linked to the `Employee` table.

### Table 2: Employee
- **employee_id** (int): The ID of the employee.
- **name** (varchar): The name of the employee.
- **experience_years** (int): The number of years of experience of the employee.
- `employee_id` is the primary key of this table.
- `experience_years` is guaranteed to be non-null.

## Objective

Write a SQL query that:
- Calculates the average experience years of all employees for each project.
- Rounds the average to two decimal places.
- Returns the result in any order.

## Example

### Given Data

#### Project Table
| project_id | employee_id |
|------------|-------------|
| 1          | 1           |
| 1          | 2           |
| 1          | 3           |
| 2          | 1           |
| 2          | 4           |

#### Employee Table
| employee_id | name   | experience_years |
|-------------|--------|------------------|
| 1           | Khaled | 3                |
| 2           | Ali    | 2                |
| 3           | John   | 1                |
| 4           | Doe    | 2                |

### Expected Output
| project_id | average_years |
|------------|---------------|
| 1          | 2.00          |
| 2          | 2.50          |

### Explanation
- For the first project (ID 1), the average experience years is calculated as \((3 + 2 + 1) / 3 = 2.00\).
- For the second project (ID 2), the average is \((3 + 2) / 2 = 2.50\).

## Solution 

```sql
SELECT *
FROM Project p
Left Join Employee e ON e.employee_id = p.employee_id
```
| project_id | employee_id | employee_id | name   | experience_years |
| ---------- | ----------- | ----------- | ------ | ---------------- |
| 1          | 1           | 1           | Khaled | 3                |
| 1          | 2           | 2           | Ali    | 2                |
| 1          | 3           | 3           | John   | 1                |
| 2          | 1           | 1           | Khaled | 3                |
| 2          | 4           | 4           | Doe    | 2                |

* LEFT JOIN the tables together to analyze all the columns together.


```sql
SELECT project_id, ROUND(IFNULL(AVG(experience_years),0),2) AS average_years
FROM Project p
Left Join Employee e ON e.employee_id = p.employee_id
GROUP BY project_id
```

| project_id | average_years |
|------------|---------------|
| 1          | 2.00          |
| 2          | 2.50          |

## Notes
- The query uses an LEFT JOIN to combine data from `Project` and `Employee` tables based on `employee_id`.
- The `AVG` function calculates the average experience years.
- The `ROUND` function is used to round the average to two decimal places.
- The `GROUP BY` clause groups the results by `project_id`.