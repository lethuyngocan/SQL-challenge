# INTRODUCTION:
Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

# PROBLEM STATEMENT:
Because Danny had a few years of experience as a data scientist - he was very aware that data collection was going to be critical for his business’ growth.

He has prepared for us an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operation
![download (1)](https://github.com/lethuyngocan/SQL-challenge/assets/94544459/5e28675f-51cf-49bd-b446-47fee97c9195)

## CASE STUDY QUESTION:
This case study has LOTS of questions - they are broken up by area of focus including:

Pizza Metrics
Runner and Customer Experience
Ingredient Optimisation
Pricing and Ratings
Bonus DML Challenges (DML = Data Manipulation Language)

## CHALLENGE:
You can find the all the details for this challenge including the datasets [here](https://8weeksqlchallenge.com/case-study-2/).

## SOLUTION:
A) DATA CLEANING AND TRANSFORMATION:
a) Table Customer_order
Looking at the `customer_orders` table below, we can see that there are
- In the `exclusions` column, there are missing/ blank spaces ' ' and null values. 
- In the `extras` column, there are missing/ blank spaces ' ' and null values.

![image](https://github.com/yaswanthteja/SQL_Dannys_Pizza_Runner_CaseStudy2-/blob/master/images/Data%20Cleaning%20%26%20Transformation/solution1.png)

If we inspect the exclusions and extras columns, we see that not every cell has a value. They either have the Null data type (i.e. [null]), null as a string (i.e. null) or are left blank. Let's clean:
````sql
DROP TABLE IF EXISTS customer_orders_temp;
CREATE TABLE customer_orders_temp AS
  SELECT 
    order_id, 
    customer_id, 
    pizza_id, 
    CASE
      WHEN exclusions is null OR exclusions = ‘null’ THEN ‘’
      ELSE exclusions
    END AS exclusions,
    CASE
      WHEN extras is null OR extras = ‘null’ THEN ‘’
      ELSE extras
    END AS extras,
    order_time
FROM customer_orders
````

b) Table runner_orders
Inspecting the `runner_orders` table below, we can see that pickup_time, distance and duration columns were created with the character varying data type (i.e. varchar) when it should have been timestamp, numeric and integer respectively.es

![image](https://github.com/yaswanthteja/SQL_Dannys_Pizza_Runner_CaseStudy2-/blob/master/images/Data%20Cleaning%20%26%20Transformation/solution3.png)

In addition, we have to remove the ‘km’, ‘minute’, ‘minutes’, ‘mins’ from distanceand duration columns.
The cancellation column has the same issues with the exclusions and extras columns in customer_orders.

````sql
DROP TABLE IF EXISTS runner_orders_temp;
CREATE TEMP TABLE runner_orders_temp AS
 SELECT 
   order_id, 
   runner_id, 
   CASE
     WHEN pickup_time = ‘null’ THEN NULL
     ELSE pickup_time
   END AS pickup_time,
   CASE
     WHEN distance = ‘null’ THEN NULL
     WHEN distance LIKE ‘%km’ THEN TRIM(‘km’ from distance)
     ELSE distance 
   END AS distance,
   CASE
     WHEN duration = ‘null’ THEN NULL
     WHEN duration LIKE ‘%mins’ THEN TRIM(‘mins’ from duration)
     WHEN duration LIKE ‘%minute’ THEN TRIM(‘minute’ from duration)
     WHEN duration LIKE ‘%minutes’ THEN TRIM(‘minutes’ from duration)
     ELSE duration
   END AS duration,
   CASE
     WHEN cancellation IS NULL or cancellation = ‘null’ THEN ‘’
     ELSE cancellation
   END AS cancellation
  FROM runner_orders
````
We now alter the data type as shown below:
````sql
ALTER TABLE runner_orders_temp
ALTER COLUMN pickup_time DATETIME,
ALTER COLUMN distance FLOAT,
ALTER COLUMN duration INT
````
To view the table after cleaning: 
````sql
SELECT * FROM runner_orders_temp
````
# B. Pizza Metrics
1) How many pizzas were ordered?
````sql
SELECT COUNT(pizza_id) AS ordered_pizza 
FROM customer_orders_temp;
`````
Answer: 
| ordered_pizza | 
| ----------- | 
|14|

2) How many unique customer orders were made?
````sql
SELECT COUNT(DISTINCT(order_id)) AS unique_customer_orders 
FROM customer_orders_temp;

````
Answer: 
|unique_customer_orders | 
| ----------- | 
|10|

3) How many successful orders were delivered by each runner?
````sql
SELECT runner_id, COUNT(runner_id) AS order_count 
FROM runner_orders_temp
WHERE distance !=' '
GROUP BY runner_id
ORDER BY order_count DESC;
````
Answer:
|runner_id	|order_count|
|---------|--------|
|1|	4|
|2|	3|
|3|	1|
* Runner 1 has 4 successful orders.
* Runner 2 has 3 successful orders.
* Runner 3 has 1 successful  order.
  
4) How many of each type of pizza was delivered?
````sql
SELECT c.pizza_id, count(c.pizza_id)
FROM runner_orders_temp AS r
INNER JOIN customer_orders_temp AS c ON r.order_id = c.order_id
WHERE distance != ' '
GROUP BY pizza_id
ORDER BY pizza_id;
````
|pizza_id	|count|
|---------|--------|
|1|9|
|2|3|

5) How many Vegetarian and Meatlovers were ordered by each customer?
````sql
SELECT c.customer_id, p.pizza_name, COUNT(p.pizza_name) AS count_pizza

FROM customer_orders_temp AS c
INNER JOIN pizza_runner.pizza_names AS p ON c.pizza_id=p.pizza_id
GROUP BY c.customer_id, p.pizza_name
ORDER BY c.customer_id;
````
Answer:
|customer_id|	pizza_name|	count_pizza|
|----------|-----------|------------|
|101|	Meatlovers|	2|
|101|	Vegetarian|	1|
|102|	Meatlovers|	2|
|102|	Vegetarian|	1|
|103|	Meatlovers|	3|
|103|	Vegetarian|	1|
|104|	Meatlovers|	3|
|105|	Vegetarian|	1|
