# Cinema Table Specification - Not Boring Movies - Basic Aggregate Functions

November, 06, 2023

[LeetCode Question](https://leetcode.com/problems/not-boring-movies/description/?envType=study-plan-v2&envId=top-sql-50)

[cat](https://i.pinimg.com/originals/5b/9c/0f/5b9c0fa145a26cbe3b5e3aafb621d640.gif)

The `Cinema` table has the following structure:

- `id` (int): Primary key, a unique identifier for each movie.
- `movie` (varchar): Name of the movie.
- `description` (varchar): A short description of the movie.
- `rating` (float): The movie's rating, represented as a float with two decimal places, ranging from 0 to 10.

## Problem Statement

We need to query the `Cinema` table to find all movies that have an odd-numbered `id` and a `description` that is not equal to "boring". The results should be ordered by the `rating` in descending order.

## Example Query Result

### Input:

Consider the following content of the `Cinema` table:

| id | movie      | description | rating |
|----|------------|-------------|--------|
| 1  | War        | great 3D    | 8.9    |
| 2  | Science    | fiction     | 8.5    |
| 3  | irish      | boring      | 6.2    |
| 4  | Ice song   | Fantasy     | 8.6    |
| 5  | House card | Interesting | 9.1    |

### Output:

The query should return the following movies, based on the aforementioned criteria:

| id | movie      | description | rating |
|----|------------|-------------|--------|
| 5  | House card | Interesting | 9.1    |
| 1  | War        | great 3D    | 8.9    |

### Explanation:

- Movies with odd-numbered IDs: 1, 3, and 5.
- Movie with ID 3 has a description "boring", which is excluded.
- Remaining movies are ordered by `rating` in descending order, resulting in IDs 5 and 1.

---

### Answer: 

```sql
SELECT * FROM Cinema
WHERE MOD(id, 2) = 1 AND description <> 'boring'
ORDER BY rating DESC;
```

* Sorting out odd numbers: (using the `MOD(id, 2) = 1` condition) 
* Sorting out how the description is not boring = using <> 






```sql 
SELECT * 
FROM Cinema 
WHERE id % 2 <> 0 AND description <> "boring"
ORDER BY rating DESC;
```
