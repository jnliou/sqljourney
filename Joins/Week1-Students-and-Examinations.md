1280. Students and Examinations-Joins - November 5, 2023

[LeetCode Question](https://leetcode.com/problems/students-and-examinations/description/?envType=study-plan-v2&envId=top-sql-50)

## Tables Structure

### Students

| Column Name   | Type    |
|---------------|---------|
| student_id    | int     |
| student_name  | varchar |

- `student_id` is the primary key for this table.
- Each row of this table contains the ID and the name of one student in the school.

### Subjects

| Column Name  | Type    |
|--------------|---------|
| subject_name | varchar |

- `subject_name` is the primary key for this table.
- Each row of this table contains the name of one subject in the school.

### Examinations

| Column Name  | Type    |
|--------------|---------|
| student_id   | int     |
| subject_name | varchar |

- There is no primary key for this table, and it may contain duplicates.
- Each student from the Students table takes every course from the Subjects table.
- Each row of this table indicates that a student with ID `student_id` attended the exam of `subject_name`.

## Challenge

Write a SQL query to find the number of times each student attended each exam. Return the result table ordered by `student_id` and `subject_name`.

## Example

### Input

**Students table:**

| student_id | student_name |
|------------|--------------|
| 1          | Alice        |
| 2          | Bob          |
| 13         | John         |
| 6          | Alex         |

**Subjects table:**

| subject_name |
|--------------|
| Math         |
| Physics      |
| Programming  |

**Examinations table:**

| student_id | subject_name |
|------------|--------------|
| 1          | Math         |
| 1          | Physics      |
| 1          | Programming  |
| 2          | Programming  |
| 1          | Physics      |
| 1          | Math         |
| 13         | Math         |
| 13         | Programming  |
| 13         | Physics      |
| 2          | Math         |
| 1          | Math         |

### Output

| student_id | student_name | subject_name | attended_exams |
|------------|--------------|--------------|----------------|
| 1          | Alice        | Math         | 3              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Programming  | 1              |
| 2          | Bob          | Math         | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Programming  | 1              |
| 6          | Alex         | Math         | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Programming  | 0              |
| 13         | John         | Math         | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Programming  | 1              |

### Explanation

- The result table should contain all students and all subjects.
- Alice attended the Math exam 3 times, the Physics exam 2 times, and the Programming exam 1 time.
- Bob attended the Math exam 1 time, the Programming exam 1 time, and did not attend the Physics exam.
- Alex did not attend any exams.
- John attended the Math exam 1 time, the Physics exam 1 time, and the Programming exam 1 time.

## Solution

## Step 1:

```sql
SELECT * 
FROM Students s
LEFT JOIN Examinations e ON s.student_id = e.student_id
```
* Joins the Student and Examination tables together on student_id 

| student_id | student_name | student_id | subject_name |
| ---------- | ------------ | ---------- | ------------ |
| 1          | Alice        | 1          | Math         |
| 1          | Alice        | 1          | Math         |
| 1          | Alice        | 1          | Physics      |
| 1          | Alice        | 1          | Programming  |
| 1          | Alice        | 1          | Physics      |
| 1          | Alice        | 1          | Math         |
| 2          | Bob          | 2          | Math         |
| 2          | Bob          | 2          | Programming  |
| 13         | John         | 13         | Physics      |
| 13         | John         | 13         | Programming  |
| 13         | John         | 13         | Math         |
| 6          | Alex         | null       | null         |

## Step 2:

```sql 
SELECT s.student_id,  s.student_name, subject_name
FROM Students s
LEFT JOIN Examinations e ON s.student_id = e.student_id
```

* Cleans up the table to only include student_id, student_name, and subject_name

| student_id | student_name | subject_name |
| ---------- | ------------ | ------------ |
| 1          | Alice        | Math         |
| 1          | Alice        | Math         |
| 1          | Alice        | Physics      |
| 1          | Alice        | Programming  |
| 1          | Alice        | Physics      |
| 1          | Alice        | Math         |
| 2          | Bob          | Math         |
| 2          | Bob          | Programming  |
| 13         | John         | Physics      |
| 13         | John         | Programming  |
| 13         | John         | Math         |
| 6          | Alex         | null         |


## Step 3: Understanding `CROSS JOIN`

The initial attempt to join the tables did not yield the expected results:

```sql
SELECT s.student_id, s.student_name, subject_name, COUNT(subject_name)
FROM Students s
LEFT JOIN Examinations e ON s.student_id = e.student_id
GROUP BY s.student_id, subject_name;
```

This query incorrectly attempts to count the number of subjects per student without including the `Subjects` table, leading to incomplete data. The result set looked like this:

| student_id | student_name | subject_name | COUNT(subject_name) |
|------------|--------------|--------------|---------------------|
| 1          | Alice        | Math         | 3                   |
| 1          | Alice        | Physics      | 2                   |
| 1          | Alice        | Programming  | 1                   |
| 2          | Bob          | Math         | 1                   |
| 2          | Bob          | Programming  | 1                   |
| 13         | John         | Physics      | 1                   |
| 13         | John         | Programming  | 1                   |
| 13         | John         | Math         | 1                   |
| 6          | Alex         | null         | 0                   |

Notice how Alex is associated with a `null` subject_name, indicating no subjects linked to him.

### Exploring `CROSS JOIN`

A `CROSS JOIN` in SQL is used to produce a Cartesian product of the rows from the tables involved in the join. Each row from the first table is combined with each row from the second table, resulting in every possible combination of rows.

The motivation behind using a `CROSS JOIN` here is to ensure that every student is linked to every subject, regardless of whether their name appears in the `Examinations` table. This is particularly useful for scenarios where we want to see all potential student and subject combinations.

Here's how a `CROSS JOIN` can be applied to our tables:

```sql
SELECT 
    Students.student_id, 
    Students.student_name, 
    Subjects.subject_name
FROM 
    Students
CROSS JOIN 
    Subjects;
```

This query will produce a result where each student is associated with every subject:

| student_id | student_name | student_id | subject_name | subject_name |
| ---------- | ------------ | ---------- | ------------ | ------------ |
| 6          | Alex         | 1          | Math         | Math         |
| 6          | Alex         | 1          | Math         | Physics      |
| 6          | Alex         | 1          | Math         | Programming  |
| 13         | John         | 1          | Math         | Math         |
| 13         | John         | 1          | Math         | Physics      |
| 13         | John         | 1          | Math         | Programming  |
| 2          | Bob          | 1          | Math         | Math         |
| 2          | Bob          | 1          | Math         | Physics      |
| 2          | Bob          | 1          | Math         | Programming  |
| 1          | Alice        | 1          | Math         | Math         |
| 1          | Alice        | 1          | Math         | Physics      |
| 1          | Alice        | 1          | Math         | Progra...

By using a `CROSS JOIN`, we can see that Alex is now linked to all subjects, which is a more accurate reflection of the school's enrollment possibilities.

## Step 6: 

* Encorporating Cross JOIN on Students and Subject table and LEFT JOIN on Examination Table
* LEFT JOIN on Examinations:

The LEFT JOIN is then used to bring in the Examinations data. This type of join is chosen because it allows all the combinations from the CROSS JOIN to remain in the result set, even if there is no matching record in the Examinations table. If a student has not taken an exam in a subject, the LEFT JOIN will still include that student-subject pair in the results, but with NULL values for any columns coming from the Examinations table.

This allows for us to identify what exams that "Alex" took, as it won't remove the Alex rows.  

```sql

SELECT *
FROM Students s
CROSS JOIN Subjects sub
LEFT JOIN Examinations e ON s.student_id = e.student_id AND sub.subject_name = e.subject_name


```

| student_id | student_name | subject_name | student_id | subject_name |
| ---------- | ------------ | ------------ | ---------- | ------------ |
| 1          | Alice        | Programming  | 1          | Programming  |
| 1          | Alice        | Physics      | 1          | Physics      |
| 1          | Alice        | Physics      | 1          | Physics      |
| 1          | Alice        | Math         | 1          | Math         |
| 1          | Alice        | Math         | 1          | Math         |
| 1          | Alice        | Math         | 1          | Math         |
| 2          | Bob          | Programming  | 2          | Programming  |
| 2          | Bob          | Physics      | null       | null         |
| 2          | Bob          | Math         | 2          | Math         |
| 13         | John         | Programming  | 13         | Programming  |
| 13         | John         | Physics      | 13         | Physics      |
| 13         | John         | Math         | 13         | Math  ...


## Step 5 Including the COUNT of exams:

```sql
SELECT s.student_id, s.student_name, sub.subject_name, COUNT(e.student_id) AS attended_exams
FROM Students s
CROSS JOIN Subjects sub
LEFT JOIN Examinations e ON s.student_id = e.student_id AND sub.subject_name = e.subject_name
GROUP BY s.student_id, s.student_name, sub.subject_name
ORDER BY s.student_id, sub.subject_name; 
```

| student_id | student_name | subject_name | attended_exams |
| ---------- | ------------ | ------------ | -------------- |
| 1          | Alice        | Math         | 3              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Programming  | 1              |
| 2          | Bob          | Math         | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Programming  | 1              |
| 6          | Alex         | Math         | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Programming  | 0              |
| 13         | John         | Math         | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Programming  | 1              |


1. **CROSS JOIN for All Combinations:**
   - `FROM Students s CROSS JOIN Subjects sub` creates a Cartesian product of all students with all subjects. This means every student is paired with every subject, regardless of whether they have attended an exam or not.

2. **LEFT JOIN for Conditional Matching:**
   - `LEFT JOIN Examinations e ON s.student_id = e.student_id AND sub.subject_name = e.subject_name` then takes this Cartesian product and attempts to find a matching record in the `Examinations` table. The `LEFT JOIN` ensures that even if there's no matching record in `Examinations` (meaning the student didn't attend an exam for a subject), the student-subject combination will still appear in the results.

3. **COUNT for Number of Exams:**
   - `COUNT(e.student_id) AS attended_exams` counts the number of times a student's ID appears in the `Examinations` table for each subject. If there's no match (the student didn't attend that exam), `e.student_id` will be `NULL`, and `COUNT` will return 0 for that student-subject pair because `COUNT` only counts non-`NULL` values.

4. **GROUP BY for Aggregation:**
   - `GROUP BY s.student_id, s.student_name, sub.subject_name` groups the results so that there's a single row for each unique combination of student and subject. This is necessary for the `COUNT` function to calculate the number of exams per student per subject.

5. **ORDER BY for Sorting:**
   - `ORDER BY s.student_id, sub.subject_name` sorts the final result set first by `student_id` and then by `subject_name`, which is a logical way to organize the data for review.

The query is designed to ensure that every student is listed with every subject from the `Subjects` table, and the `COUNT` function is used to determine how many exams each student has attended for each subject. The use of `CROSS JOIN` and `LEFT JOIN` together in this way is a clever solution to the problem of ensuring that all possible student-subject pairs are included in the output, not just those for which there is an exam record.

