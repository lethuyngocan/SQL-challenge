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
* What is the total count of transactions for each platform
* What is the percentage of sales for Retail vs Shopify for each month?
* What is the percentage of sales by demographic for each year in the dataset?
* Which age_band and demographic values contribute the most to Retail sales?
* Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?

# SOLUTION:
1) CLEANSING THE DATA: Here I create the table to summary the action we need to do in order to cleasing the data and create the new table in the data_mart schema named clean_weekly_sales
   
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

