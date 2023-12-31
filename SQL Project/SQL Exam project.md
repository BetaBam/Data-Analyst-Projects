# Practical Exam: Grocery Store Sales

FoodYum is a grocery store chain that is based in the United States.

Food Yum sells items such as produce, meat, dairy, baked goods, snacks, and other household food staples.

As food costs rise, FoodYum wants to make sure it keeps stocking products in all categories that cover a range of prices to ensure they have stock for a broad range of customers. 
## Key to complete
- Identify and replace missing value
- Convert values between data types
- Clean categorical and text data by manipulating strings
- Aggregate numeric, categorical variables and dates by groups
- Extract data based on different conditions
## Data

The data is available in the table `products`.

The dataset contains records of customers for their last full year of the loyalty program.

| Column Name | Criteria                                                |
|-------------|---------------------------------------------------------|
|product_id | Nominal. The unique identifier of the product. </br>Missing values are not possible due to the database structure.|
| product_type | Nominal. The product category type of the product, one of 5 values (Produce, Meat, Dairy, Bakery, Snacks). </br>Missing values should be replaced with “Unknown”. |
| brand | Nominal. The brand of the product. One of 7 possible values. </br>Missing values should be replaced with “Unknown”. |
| weight | Continuous. The weight of the product in grams. This can be any positive value, rounded to 2 decimal places. </br>Missing values should be replaced with the overall median weight. |
| price | Continuous. The price the product is sold at, in US dollars. This can be any positive value, rounded to 2 decimal places. </br>Missing values should be replaced with the overall median price. |
| average_units_sold | Discrete. The average number of units sold each month. This can be any positive integer value. </br>Missing values should be replaced with 0. |
| year_added | Nominal. The year the product was first added to FoodYum stock.</br>Missing values should be replaced with 2022. |
| stock_location | Nominal. The location that stock originates. This can be one of four warehouse locations, A, B, C or D </br>Missing values should be replaced with “Unknown”. |
# Task 1

Last year (2022) there was a bug in the product system. For some products that were added in that year, the `year_added` value was not set in the data. As the year the product was added may have an impact on the price of the product, this is important information to have. 

Write a query to determine how many products have the `year_added` value missing. Your output should be a single column, `missing_year`, with a single row giving the number of missing values.

```
-- Query result for Task 1
SELECT COUNT(*) AS missing_year
FROM products
WHERE year_added IS NULL;
```
![image](https://github.com/BetaBam/Data-Analyst-Projects/assets/150643263/5701fe77-00f6-41fe-9c0d-cf2592de9420)
# Task 2

Given what you know about the year added data, you need to make sure all of the data is clean before you start your analysis. The table below shows what the data should look like. 

Write a query to ensure the product data matches the description provided. Do not update the original table.  

| Column Name | Criteria                                                |
|-------------|---------------------------------------------------------|
|product_id | Nominal. The unique identifier of the product. </br>Missing values are not possible due to the database structure.|
| product_type | Nominal. The product category type of the product, one of 5 values (Produce, Meat, Dairy, Bakery, Snacks). </br>Missing values should be replaced with “Unknown”. |
| brand | Nominal. The brand of the product. One of 7 possible values. </br>Missing values should be replaced with “Unknown”. |
| weight | Continuous. The weight of the product in grams. This can be any positive value, rounded to 2 decimal places. </br>Missing values should be replaced with the overall median weight. |
| price | Continuous. The price the product is sold at, in US dollars. This can be any positive value, rounded to 2 decimal places. </br>Missing values should be replaced with the overall median price. |
| average_units_sold | Discrete. The average number of units sold each month. This can be any positive integer value. </br>Missing values should be replaced with 0. |
| year_added | Nominal. The year the product was first added to FoodYum stock.</br>Missing values should be replaced with last year (2022). |
| stock_location | Nominal. The location that stock originates. This can be one of four warehouse locations, A, B, C or D </br>Missing values should be replaced with “Unknown”. |
```
-- Query result for Task 2
SELECT
    product_id,
    COALESCE(product_type, 'Unknown') AS product_type,
    COALESCE(NULLIF(REPLACE(brand, '-', ''), ''), 'Unknown') AS brand,
    COALESCE(ROUND(CAST(REPLACE(weight, 'gram', '') AS DECIMAL(10, 2)), 2), ROUND((SELECT PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY CAST(REPLACE(weight, 'gram', '') AS DECIMAL(10, 2))) FROM products), 2)) AS weight,
    COALESCE((CAST(price AS DECIMAL(10, 2)),(CAST((SELECT PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY price) FROM products) AS DECIMAL(10, 2)))) AS price,
    COALESCE(average_units_sold, 0) AS average_units_sold,
    COALESCE(year_added, 2022) AS year_added,
    COALESCE(UPPER(stock_location), 'Unknown') AS stock_location
FROM products;
```
![image](https://github.com/BetaBam/Data-Analyst-Projects/assets/150643263/3fec5a43-a09f-4094-a6d3-11f602757eb6)
# Task 3

To find out how the range varies for each product type, your manager has asked you to determine the minimum and maximum values for each product type.   

Write a query to return the `product_type`, `min_price` and `max_price` columns. 
```
-- Query result for Task 3
SELECT product_type,
    MIN(price) AS min_price,
    MAX(price) AS max_price
FROM products
GROUP BY product_type;
```
![image](https://github.com/BetaBam/Data-Analyst-Projects/assets/150643263/86394f67-3372-4517-97c0-5ecba17e7f2c)
# Task 4

The team want to look in more detail at meat and dairy products where the average units sold was greater than ten. 

Write a query to return the `product_id`, `price` and `average_units_sold` of the rows of interest to the team. 
```
-- Query result for Task 4
SELECT
  product_id,
  price,
  average_units_sold
FROM products
WHERE (product_type = 'Meat' OR product_type = 'Dairy')
  AND average_units_sold > 10;
```
![image](https://github.com/BetaBam/Data-Analyst-Projects/assets/150643263/c7ef7bbc-264d-4933-b155-fb65281dc437)
