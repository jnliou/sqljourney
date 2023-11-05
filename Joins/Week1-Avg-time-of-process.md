Questions: Average Time of Process per Machine - Completed November 04, 2023

[LeetCode Question](https://leetcode.com/problems/average-time-of-process-per-machine/description/?envType=study-plan-v2&envId=top-sql-50)

Table: Activity


| Column Name    | Type    |
|----------------|---------|
| machine_id     | int     |
| process_id     | int     |
| activity_type  | enum    |
| timestamp      | float   |

The table shows the user activities for a factory website.
(machine_id, process_id, activity_type) is the primary key (combination of columns with unique values) of this table.
machine_id is the ID of a machine.
process_id is the ID of a process running on the machine with ID machine_id.
activity_type is an ENUM (category) of type ('start', 'end').
timestamp is a float representing the current time in seconds.
'start' means the machine starts the process at the given timestamp and 'end' means the machine ends the process at the given timestamp.
The 'start' timestamp will always be before the 'end' timestamp for every (machine_id, process_id) pair.
 

There is a factory website that has several machines each running the same number of processes. Write a solution to find the average time each machine takes to complete a process.

The time to complete a process is the 'end' timestamp minus the 'start' timestamp. The average time is calculated by the total time to complete every process on the machine divided by the number of processes that were run.

The resulting table should have the machine_id along with the average time as processing_time, which should be rounded to 3 decimal places.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Activity table:

| machine_id | process_id | activity_type | timestamp |
|------------|------------|---------------|-----------|
| 0          | 0          | start         | 0.712     |
| 0          | 0          | end           | 1.520     |
| 0          | 1          | start         | 3.140     |
| 0          | 1          | end           | 4.120     |
| 1          | 0          | start         | 0.550     |
| 1          | 0          | end           | 1.550     |
| 1          | 1          | start         | 0.430     |
| 1          | 1          | end           | 1.420     |
| 2          | 0          | start         | 4.100     |
| 2          | 0          | end           | 4.512     |
| 2          | 1          | start         | 2.500     |
| 2          | 1          | end           | 5.000     |

Output: 

| machine_id | processing_time |
|------------|-----------------|
| 0          | 0.894           |
| 1          | 0.995           |
| 2          | 1.456           |

Explanation: 
There are 3 machines running 2 processes each.
Machine 0's average time is ((1.520 - 0.712) + (4.120 - 3.140)) / 2 = 0.894
Machine 1's average time is ((1.550 - 0.550) + (1.420 - 0.430)) / 2 = 0.995
Machine 2's average time is ((4.512 - 4.100) + (5.000 - 2.500)) / 2 = 1.456

Answer/Thought Process: 

* Create a self  join for all the columns in the Activity table
* Separate the activity type into one table column for "end" (a.activity_type) and "start" (b.activity_type)
```sql
SELECT 
a.machine_id, a.process_id, b.machine_id, b.process_id, a.activity_type, b.activity_type, a.timestamp, b.timestamp
From Activity a
JOIN Activity b
ON a.machine_id = b.machine_id AND a.process_id = b.process_id 
AND a.activity_type = "end" AND b.activity_type = "start"
```

| machine_id | process_id | activity_type | timestamp | machine_id | process_id | activity_type | timestamp |
| ---------- | ---------- | ------------- | --------- | ---------- | ---------- | ------------- | --------- |
| 0          | 0          | end           | 1.52      | 0          | 0          | start         | 0.712     |
| 0          | 1          | end           | 4.12      | 0          | 1          | start         | 3.14      |
| 1          | 0          | end           | 1.55      | 1          | 0          | start         | 0.55      |
| 1          | 1          | end           | 1.42      | 1          | 1          | start         | 0.43      |
| 2          | 0          | end           | 4.512     | 2          | 0          | start         | 4.1       |
| 2          | 1          | end           | 5         | 2          | 1          | start         | 2.5       |

* Create a column that takes the average of the processing time by substracting a.timestamp - b.timestamp

```sql
SELECT 
a.machine_id, a.process_id, a.activity_type, b.activity_type, a.timestamp, b.timestamp, AVG(a.timestamp-b.timestamp) as processing_time
From Activity a
JOIN Activity b
ON a.machine_id = b.machine_id AND a.process_id = b.process_id 
AND a.activity_type = "end" AND b.activity_type = "start"
GROUP BY 1,2,3,4
```

| machine_id | process_id | activity_type | activity_type | timestamp | timestamp | (a.timestamp-b.timestamp) |
| ---------- | ---------- | ------------- | ------------- | --------- | --------- | ------------------------- |
| 0          | 0          | end           | start         | 1.52      | 0.712     | 0.8079999685287476        |
| 0          | 1          | end           | start         | 4.12      | 3.14      | 0.9799997806549072        |
| 1          | 0          | end           | start         | 1.55      | 0.55      | 0.9999999403953552        |
| 1          | 1          | end           | start         | 1.42      | 0.43      | 0.9899999499320984        |
| 2          | 0          | end           | start         | 4.512     | 4.1       | 0.4120001792907715        |
| 2          | 1          | end           | start         | 5         | 2.5       | 2.5                       |

#GroupBy - machine_id, process_id, activity_type 


* Query to look at the average processing time, and remove the timestamp column

```sql
SELECT 
a.machine_id, a.process_id, a.activity_type, b.activity_type, AVG(a.timestamp-b.timestamp) as processing_time
From Activity a
JOIN Activity b
ON a.machine_id = b.machine_id AND a.process_id = b.process_id 
AND a.activity_type = "end" AND b.activity_type = "start"
GROUP BY 1,2,3,4
```

| machine_id | process_id | activity_type | activity_type | processing_time    |
| ---------- | ---------- | ------------- | ------------- | ------------------ |
| 0          | 0          | end           | start         | 0.8079999685287476 |
| 0          | 1          | end           | start         | 0.9799997806549072 |
| 1          | 0          | end           | start         | 0.9999999403953552 |
| 1          | 1          | end           | start         | 0.9899999499320984 |
| 2          | 0          | end           | start         | 0.4120001792907715 |
| 2          | 1          | end           | start         | 2.5                |


FINAL Answer:


* Query to look at the rounded average processing time (to 3 decimal places) and group by machine_id. 

```sql
SELECT 
a.machine_id, ROUND(AVG(a.timestamp-b.timestamp),3) as processing_time 
From Activity a
JOIN Activity b
ON a.machine_id = b.machine_id AND a.process_id = b.process_id 
AND a.activity_type = "end" AND b.activity_type = "start"
GROUP BY 1
```
*** 


| machine_id | processing_time |
|------------| ----------------|
| 0          | 0.894           |
| 1          | 0.995           |
| 2          | 1.456           |