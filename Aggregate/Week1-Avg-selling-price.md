## Average Selling Price 
[LeetCode Question](https://leetcode.com/problems/average-selling-price/description/?envType=study-plan-v2&envId=top-sql-50)

![Grocery](https://media.tenor.com/OmXCcN0aYoEAAAAi/supermarket-market.gif "grocery")

November 09, 2023

## Tables Overview

1. **Prices Table**: Stores price information for products over specific time periods.

   - **Columns**:
     - `product_id` (int): Unique identifier for products.
     - `start_date` (date): The beginning date of the pricing period.
     - `end_date` (date): The ending date of the pricing period.
     - `price` (int): The price of the product during the given period.
   - **Primary Key**: Combination of (`product_id`, `start_date`, `end_date`).
   - **Constraints**: For each `product_id`, periods defined by `start_date` and `end_date` do not overlap.

2. **UnitsSold Table**: Records the sales data of products.

   - **Columns**:
     - `product_id` (int): Identifier for products, correlating to `product_id` in the Prices table.
     - `purchase_date` (date): The date when the product was sold.
     - `units` (int): The number of units sold on the `purchase_date`.
   - **Notes**: This table may contain duplicate rows.

## Objective

Write a SQL solution to calculate the average selling price for each product. The average price should be rounded to 2 decimal places and presented in any order.

## Expected Result

The result should be a table with two columns:

- `product_id`: Identifier for the product.
- `average_price`: Calculated average selling price for the product, rounded to 2 decimal places.

## Calculation Method

The average selling price for each product is calculated as follows:

1. **Total Price Calculation**: For each product, calculate the total price by multiplying the units sold (from the UnitsSold table) with the corresponding price (from the Prices table) within the respective date ranges.

2. **Average Price**: Divide the total price by the total number of units sold for each product.

## Example

### Input

#### Prices Table
| product_id | start_date | end_date   | price |
|------------|------------|------------|-------|
| 1          | 2019-02-17 | 2019-02-28 | 5     |
| 1          | 2019-03-01 | 2019-03-22 | 20    |
| 2          | 2019-02-01 | 2019-02-20 | 15    |
| 2          | 2019-02-21 | 2019-03-31 | 30    |

#### UnitsSold Table
| product_id | purchase_date | units |
|------------|---------------|-------|
| 1          | 2019-02-25    | 100   |
| 1          | 2019-03-01    | 15    |
| 2          | 2019-02-10    | 200   |
| 2          | 2019-03-22    | 30    |

### Output

| product_id | average_price |
|------------|---------------|
| 1          | 6.96          |
| 2          | 16.96         |

### Explanation

- For product 1: The total price is (100 units * $5) + (15 units * $20), with a total of 115 units. The average price is (500 + 300) / 115 = $6.96.
- For product 2: The total price is (200 units * $15) + (30 units * $30), with a total of 230 units. The average price is (3000 + 900) / 230 = $16.96.


Write a solution to find the average selling price for each product. average_price should be rounded to 2 decimal places.

Return the result table in any order.

## LEFT JOIN
```sql
SELECT * 
FROM Prices p
LEFT JOIN UnitsSold u on p.product_id = u.product_id
```

| product_id | start_date | end_date   | price | product_id | purchase_date | units |
| ---------- | ---------- | ---------- | ----- | ---------- | ------------- | ----- |
| 1          | 2019-02-17 | 2019-02-28 | 5     | 1          | 2019-03-01    | 15    |
| 1          | 2019-02-17 | 2019-02-28 | 5     | 1          | 2019-02-25    | 100   |
| 1          | 2019-03-01 | 2019-03-22 | 20    | 1          | 2019-03-01    | 15    |
| 1          | 2019-03-01 | 2019-03-22 | 20    | 1          | 2019-02-25    | 100   |
| 2          | 2019-02-01 | 2019-02-20 | 15    | 2          | 2019-03-22    | 30    |
| 2          | 2019-02-01 | 2019-02-20 | 15    | 2          | 2019-02-10    | 200   |
| 2          | 2019-02-21 | 2019-03-31 | 30    | 2          | 2019-03-22    | 30    |
| 2          | 2019-02-21 | 2019-03-31 | 30    | 2          | 2019-02-10    | 200   |

In this query, the LEFT JOIN is used without an additional condition in the join clause. It joins the Prices and UnitsSold tables based on product_id. This approach includes all records from the Prices table and matches them with any records in the UnitsSold table that have the same product_id. The join does not consider the date range (start_date and end_date). Therefore, you will see all possible combinations of Prices and UnitsSold records that have the same product_id, regardless of the purchase date.


## INNER JOIN
```sql 
SELECT *
FROM Prices p
INNER JOIN UnitsSold u on p.product_id = u.product_id
```

| product_id | start_date | end_date   | price | product_id | purchase_date | units |
| ---------- | ---------- | ---------- | ----- | ---------- | ------------- | ----- |
| 1          | 2019-03-01 | 2019-03-22 | 20    | 1          | 2019-02-25    | 100   |
| 1          | 2019-02-17 | 2019-02-28 | 5     | 1          | 2019-02-25    | 100   |
| 1          | 2019-03-01 | 2019-03-22 | 20    | 1          | 2019-03-01    | 15    |
| 1          | 2019-02-17 | 2019-02-28 | 5     | 1          | 2019-03-01    | 15    |
| 2          | 2019-02-21 | 2019-03-31 | 30    | 2          | 2019-02-10    | 200   |
| 2          | 2019-02-01 | 2019-02-20 | 15    | 2          | 2019-02-10    | 200   |
| 2          | 2019-02-21 | 2019-03-31 | 30    | 2          | 2019-03-22    | 30    |
| 2          | 2019-02-01 | 2019-02-20 | 15    | 2          | 2019-03-22    | 30    |

## Query with LEFT JOIN and AND in the Join Condition:

* Querying for start date and end date

```sql SELECT *
FROM Prices p
LEFT JOIN UnitsSold u ON p.product_id = u.product_id
AND u.purchase_date BETWEEN start_date AND end_date
```

| product_id | start_date | end_date   | price | product_id | purchase_date | units |
| ---------- | ---------- | ---------- | ----- | ---------- | ------------- | ----- |
| 1          | 2019-02-17 | 2019-02-28 | 5     | 1          | 2019-02-25    | 100   |
| 1          | 2019-03-01 | 2019-03-22 | 20    | 1          | 2019-03-01    | 15    |
| 2          | 2019-02-01 | 2019-02-20 | 15    | 2          | 2019-02-10    | 200   |
| 2          | 2019-02-21 | 2019-03-31 | 30    | 2          | 2019-03-22    | 30    |

* The LEFT JOIN ensures all records from the Prices table are included, even if there is no matching record in the UnitsSold table. However, the additional condition (AND u.purchase_date BETWEEN start_date AND end_date) restricts the rows from UnitsSold to only those where the purchase_date falls within the range defined by start_date and end_date in the Prices table. If no matching UnitsSold record is found within this date range, the corresponding fields from UnitsSold would be NULL in the result set.

## Solution

```sql
SELECT p.product_id, IFNULL(ROUND((SUM(p.price*units))/SUM(units),2),0) AS average_price
FROM Prices p
LEFT JOIN UnitsSold u ON p.product_id = u.product_id
AND u.purchase_date BETWEEN start_date AND end_date
GROUP BY p.product_id
```
| product_id | average_price |
| ---------- | ------------- |
| 1          | 6.96          |
| 2          | 16.96         |


## Why do we use a Left Join and not an Inner Join?

The LEFT JOIN is used when you want to include all records from the left table (Prices in this case) and the matched records from the right table (UnitsSold). If there is no match, the result set will still include the rows from the left table with NULLs for the columns of the right table. This is useful when you need a complete list from one table along with any available corresponding data from another table.

In summary, using an INNER JOIN in this context may result in incomplete or inaccurate calculations of the average selling price, as it might exclude relevant pricing periods or sales data that are crucial for an accurate analysis. A LEFT JOIN, with appropriate conditions to match sales to their respective pricing periods, is typically more suitable for this kind of analysis.