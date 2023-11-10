# Week 1 - Managers with at Least 5 Direct Reports
November 8, 2023

[LeetCode](https://leetcode.com/problems/managers-with-at-least-5-direct-reports/description/?envType=study-plan-v2&envId=top-sql-50)

## Overview

The `Employee` table is designed to maintain a simple yet comprehensive list of employees within a company. It tracks not only individual employees but also their departmental alignments and managerial relationships.

Employee =
| id  | name  | department | managerId |
| --- | ----- | ---------- | --------- |
| 101 | John  | A          | null      |
| 102 | Dan   | A          | 101       |
| 103 | James | A          | 101       |
| 104 | Amy   | A          | 101       |
| 105 | Anne  | A          | 101       |
| 106 | Ron   | B          | 101       |

## Schema

The table is comprised of four columns:

- `id`: An integer value uniquely identifying each employee within the company. It serves as the primary key for the table.
- `name`: A variable character field that stores the full name of the employee.
- `department`: A variable character field representing the department to which the employee is assigned.
- `managerId`: An integer value that references the `id` of the manager to whom the employee reports directly.

## Constraints

- The `id` field is unique across the table, ensuring that each employee has a distinct identifier.
- The `managerId` can be null, indicating that the respective employee does not have a manager.
- An employee cannot be their own manager; thus, `managerId` cannot be the same as the `id` for any row.
- The `managerId` field, when not null, will correspond to the `id` of another employee who is designated as a manager.

## Relationships

- The `managerId` field establishes a hierarchical relationship between employees, indicating who reports to whom.
- If `managerId` is null, the employee is considered to not have a manager, which might signify a top-level position, such as a CEO or a sole proprietor.

## Usage

The primary function of this table is to facilitate queries about employee-management structures, such as:

- Determining the direct reports of a particular manager.
- Understanding the departmental structure by listing employees under each department.
- Identifying managers with a specific minimum number of direct reports.

## Example Query

An example use case for this table is to find managers with at least five direct reports. A query can be crafted to return a list of such managers, displaying their names and the count of direct reports.

The output will be a simple table with the `name` of the manager(s) who meet the criteria. Here's an example of what the output might look like based on the provided data:

```
+------+
| name |
+------+
| John |
+------+
```

In the above example, 'John' is listed as a manager with at least five direct reports.

## Solution

## Step 1 - JOIN or INNER JOIN 
```sql 
SELECT *
FROM Employee a 
JOIN Employee b ON a.id = b.managerId 
```

* The purpose of joining a.id onto b.managerID was so we can identify who's manager each employee reports to. 

| id  | name | department | managerId | id  | name  | department | managerId |
| --- | ---- | ---------- | --------- | --- | ----- | ---------- | --------- |
| 101 | John | A          | null      | 102 | Dan   | A          | 101       |
| 101 | John | A          | null      | 103 | James | A          | 101       |
| 101 | John | A          | null      | 104 | Amy   | A          | 101       |
| 101 | John | A          | null      | 105 | Anne  | A          | 101       |
| 101 | John | A          | null      | 106 | Ron   | B          | 101       |

```sql

SELECT a.name 
FROM Employee a 
JOIN Employee b ON a.id = b.managerId 
GROUP BY b.managerId 
HAVING COUNT(*) >= 5
```

* We want to ensure we SELECT *a.name*, as the output will be just the name of the manager that people report to. 
* 

## NOTES

INNER JOIN: This is a type of join that returns rows from both tables that satisfy the given condition. If there is no match, the result set will not include rows from either table. It’s the most common type of join.

JOIN: By default, without any other keyword, JOIN is equivalent to INNER JOIN. In other words, if you write SELECT * FROM table1 JOIN table2 ON table1.column_name = table2.column_name; it defaults to an INNER JOIN.