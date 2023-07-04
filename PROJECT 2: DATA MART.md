# DATA MART
![5](https://github.com/lethuyngocan/SQL-challenge/assets/94544459/64cb66e9-e59b-4172-b7c9-18674bde9fb8)
                                  <h1 align="center">View the case study [link](https://8weeksqlchallenge.com/case-study-5/) here.</h1>
                                 

# TABLE OF CONTENT:
- [INTRODUCTION](#INTRODUCTION)
- [PROBLEM STATEMENT](#PROBLEM-STATEMENT)
- [BUSINESS QUESTION](#BUSINESS-QUESTION)
- [SOLUTION](#SOLUTION)

# INTRODUCTION:
Data Mart is Danny’s latest venture and after running international operations for his online supermarket that specialises in fresh produce - Danny is asking for your support to analyse his sales performance.

The key business question he wants you to help him answer are the following:

* What was the quantifiable impact of the changes introduced in June 2020?
* Which platform, region, segment and customer types were the most impacted by this change?
* What can we do about future introduction of similar sustainability updates to the business to minimise impact on sales?

# PROBLEM STATEMENT:
This case study actually is based off a real life change in Australia retailers where plastic bags were no longer provided for free - as you can expect, some customers would have changed their shopping behaviour because of this change!
* AVAILABLE DATA:
For this case study there is only a single table: data_mart.weekly_sales
The Entity Relationship Diagram is shown below with the data types made clear, please note that there is only this one table - hence why it looks a little bit lonely!
![case-study-5-erd](https://github.com/lethuyngocan/SQL-challenge/assets/94544459/0dceb97d-62d1-43a9-a2d6-2670286914a0)

# BUSINESS QUESTION
1. DATA CLEANSING STEP:
* In a single query, perform the following operations and generate a new table in the data_mart schema named clean_weekly_sales:

* Convert the week_date to a DATE format

* Add a week_number as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc

* Add a month_number with the calendar month for each week_date value as the 3rd column

* Add a calendar_year column as the 4th column containing either 2018, 2019 or 2020 values

* Add a new column called age_band after the original segment column using the following mapping on the number inside the segment value

segment|	age_band
-------|----------
1|	Young Adults
2|	Middle Aged
3 or 4|	Retirees
* Add a new demographic column using the following mapping for the first letter in the segment values:
  
segment|	demographic
-------|-------------
C	|Couples
F	|Families
* Ensure all null string values with an "unknown" string value in the original segment column as well as the new age_band and demographic columns

* Generate a new avg_transaction column as the sales value divided by transactions rounded to 2 decimal places for each record

2. DATA EXPLORATION:
* What day of the week is used for each week_date value?
* What range of week numbers are missing from the dataset?
* How many total transactions were there for each year in the dataset?
* What is the total sales for each region for each month?
* What is the total count of transactions for each platform?
* What is the percentage of sales for Retail vs Shopify for each month?
* What is the percentage of sales by demographic for each year in the dataset?
* Which age_band and demographic values contribute the most to Retail sales?
* Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?

# SOLUTION:
<h1 align="center">CLEANSING THE DATA:</h1> 
  
   Here I create the table to summary the action we need to do in order to cleasing the data and create the new table in the data_mart schema named clean_weekly_sales
   
Column Name	| Requirements
------------|-------------
week_date|	Convert to DATE to DATE format
week_number|	Extract number of week from week_date
month_number|	Extract number of month from week_date
calendar_year|	Extract year from week_date
region|	-
platform|-
segment|-
age_band|	 1 = Young Adults, 2 = Middle Aged, 3/4 = Retirees and null = Unknown
demographic|	C = Couples and F = Families and null = Unknown
customer_type|-
transactions|-
avg_transaction| Sales value divided by transactions rounded to 2 decimal
sales|	-

SOLUTION:
**Schema (PostgreSQL v13)**
      
**Query #1**

    CREATE TEMP TABLE clean_weekly_sales AS (
        SELECT
            TO_DATE(week_date, 'DD/MM/YY') AS week_date,
            DATE_PART('week', TO_DATE(week_date, 'DD/MM/YY')) AS week_number,
            DATE_PART('month', TO_DATE(week_date, 'DD/MM/YY')) AS month_number,
            DATE_PART('year', TO_DATE(week_date, 'DD/MM/YY')) AS calendar_year,
            region,
            platform,
            segment,
            CASE
                WHEN RIGHT(segment,1) = '1' THEN 'Young Adults'
                WHEN RIGHT(segment,1) = '2' THEN 'Middle Aged'
                WHEN RIGHT(segment,1) IN ('3','4') THEN 'Retirees'
                ELSE 'unknown'
            END AS age_band,
            CASE
                WHEN LEFT(segment,1) = 'C' THEN 'Couples'
                WHEN LEFT(segment,1) = 'F' THEN 'Families'
                ELSE 'unknown'
            END AS demographic,
            customer_type,
            transactions,
            ROUND((sales::NUMERIC/transactions), 2) AS avg_transaction,
            sales
        FROM
            data_mart.weekly_sales
    );

    SELECT
        *
    FROM
        clean_weekly_sales
    LIMIT 5;

| week_date                | week_number | month_number | calendar_year | region | platform | segment | age_band     | demographic | customer_type | transactions | avg_transaction | sales    |
| ------------------------ | ----------- | ------------ | ------------- | ------ | -------- | ------- | ------------ | ----------- | ------------- | ------------ | --------------- | -------- |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020          | ASIA   | Retail   | C3      | Retirees     | Couples     | New           | 120631       | 30.31           | 3656163  |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020          | ASIA   | Retail   | F1      | Young Adults | Families    | New           | 31574        | 31.56           | 996575   |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020          | USA    | Retail   | null    | unknown      | unknown     | Guest         | 529151       | 31.20           | 16509610 |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020          | EUROPE | Retail   | C1      | Young Adults | Couples     | New           | 4517         | 31.42           | 141942   |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020          | AFRICA | Retail   | C2      | Middle Aged  | Couples     | New           | 58046        | 30.29           | 1758388  |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/jmnwogTsUE8hGqkZv9H7E8/8)

<h1 align="center"> DATA EXPLORATION:</h1> 

1) What day of the week is used for each week_date value?

````sql
SELECT
  DISTINCT TO_CHAR(week_date, 'Day') AS name_of_day
FROM
  data_mart.clean_weekly_sales;
````
**Answer**
| weekday |
|--|
| Monday |

2) What range of week numbers are missing from the dataset?
```sql
SELECT DISTINCT week_number
FROM clean_weekly_sales
ORDER BY week_number ASC;
````
**Answer**
| weeknumber |
|--|
| 13|
|14|
|15|
|16|
|17|
|18|
|19|
|20|
|21|
|22|
|23|
|24|
|25|
|26|
|27|
|28|
|29|
|30|
|31|
|32|
|33|
|34|
|35|
|36|

Therefore, range of week numbers missing from the dataset are: [1,12] and[35,52]
3) How many total transactions were there for each year in the dataset?
````sql
SELECT
	calendar_year,
    SUM(transactions) AS total_transaction
FROM 
	clean_weekly_sales
GROUP BY
	calendar_year
ORDER BY
	calendar_year
````
**Answer**
| calendar_year | total_transaction |
| -------------- | ------------------- |
| 2018           | 346406460           |
| 2019           | 365639285           |
| 2020           | 375813651           |

4)  What is the total sales for each region for each month?
```sql
SELECT 
   	region,
    month_number,
    SUM(sales) AS total_sales
FROM clean_weekly_sales
GROUP BY 1,2
ORDER BY 1,2
````
**Answer**
|region|	month_number|	total_sales|
|------|--------------|-----------|
|AFRICA|	3|	567767480|
|AFRICA|	4|	1911783504|
|AFRICA|	5	|1647244738|
|AFRICA|	6|1767559760|
|AFRICA|	7|	1960219710|
|AFRICA|	8|	1809596890|
|AFRICA|	9	|276320987|

Note: For illustration purpose, I only show the total_sale in each month for Africa region

5) What is the total count of transactions for each platform?
```sql
SELECT 
   	platform,
    SUM(transactions) AS total_transaction
FROM clean_weekly_sales
GROUP BY 1
````
**Answer**
| platform | total_transaction|
| -------- | ---------- |
| Shopify  | 5925169    |
| Retail   | 1081934227 |

6)  What is the percentage of sales for Retail vs Shopify for each month?
```sql
WITH cte_monthlysale AS (
SELECT 
	DATE_TRUNC('month', week_date) AS month_number,
    platform,
    SUM(sales) AS monthly_sales
FROM
	clean_weekly_sales
GROUP BY
	1,2
ORDER BY
	1,2
)
SELECT
	month_number,
    platform,
    ROUND((monthly_sales/total_sales*100),2) AS percentage
FROM
(
SELECT
	month_number,
    platform,
    monthly_sales,
    SUM(monthly_sales) OVER (PARTITION BY month_number) AS total_sales 
  
FROM
	cte_monthlysale ) AS subquery
````
**Answer**
|month_number|	platform|	percentage|
|-------------|----------|--------------|
|2018-03-01T00:00:00.000Z|	Retail|	97.92|
|2018-03-01T00:00:00.000Z|	Shopify	|2.08|
|2018-04-01T00:00:00.000Z|	Retail|	97.93|
|2018-04-01T00:00:00.000Z|	Shopify|	2.07|
|2018-05-01T00:00:00.000Z|	Retail	|97.73|
|2018-05-01T00:00:00.000Z|	Shopify|	2.27|

    
