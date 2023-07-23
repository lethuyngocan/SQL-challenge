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
1) DATA CLEANING AND TRANSFORMATION:
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

