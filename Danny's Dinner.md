# DANNY'S DINNER CHALLENGE

![127727503-9d9e7a25-93cb-4f95-8bd0-20b87cb4b459](https://github.com/lethuyngocan/SQL-challenge/assets/94544459/f333c432-cefb-426d-a9bf-2acf61d5ac1a)

# TABLE OF CONTENT
- [INTRODUCTION](#INTRODUCTION)
- [PROBLEM STATEMENT](#PROBLEM-STATEMENT)
- [BUSINESS QUESTION](#BUSINESS-QUESTION)
- [SOLUTION](#SOLUTION)

NOTE: Information regarding this case study was quoted  from the following 8 week SQL challenge. Please refer to this [link](https://8weeksqlchallenge.com/case-study-1/) to access the challenge

# INTRODUCTION: 
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.

Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.

# PROBLEM STATEMENT: 
Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

Danny has shared with you 3 key datasets for this case study:

* sales
* menu
* members
You can inspect the entity relationship diagram and example data below.
![127271130-dca9aedd-4ca9-4ed8-b6ec-1e1920dca4a8](https://github.com/lethuyngocan/SQL-challenge/assets/94544459/09ad9b5b-c645-4ece-9d1c-8d619889227b)

# BUSINESS QUESTION:
Each of the following case study questions can be answered using a single SQL statement:

1) What is the total amount each customer spent at the restaurant?
2) How many days has each customer visited the restaurant?
3) What was the first item from the menu purchased by each customer?
4) What is the most purchased item on the menu and how many times was it purchased by all customers?
5) Which item was the most popular for each customer?
6) Which item was purchased first by the customer after they became a member?
7) Which item was purchased just before the customer became a member?
8) What is the total items and amount spent for each member before they became a member?
9) If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10) In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

# SOLUTION:
1) What is the total amount each customer spent at the restaurant?
SELECT
  	s.customer_id, sum(m.price)
FROM dannys_diner.sales as s
inner join dannys_diner.menu as m
on s.product_id=m.product_id
group by s.customer_id
order by s.customer_id
**Schema (PostgreSQL v13)**

    CREATE SCHEMA dannys_diner;
    SET search_path = dannys_diner;
    
    CREATE TABLE sales (
      "customer_id" VARCHAR(1),
      "order_date" DATE,
      "product_id" INTEGER
    );
    
    INSERT INTO sales
      ("customer_id", "order_date", "product_id")
    VALUES
      ('A', '2021-01-01', '1'),
      ('A', '2021-01-01', '2'),
      ('A', '2021-01-07', '2'),
      ('A', '2021-01-10', '3'),
      ('A', '2021-01-11', '3'),
      ('A', '2021-01-11', '3'),
      ('B', '2021-01-01', '2'),
      ('B', '2021-01-02', '2'),
      ('B', '2021-01-04', '1'),
      ('B', '2021-01-11', '1'),
      ('B', '2021-01-16', '3'),
      ('B', '2021-02-01', '3'),
      ('C', '2021-01-01', '3'),
      ('C', '2021-01-01', '3'),
      ('C', '2021-01-07', '3');
     
    
    CREATE TABLE menu (
      "product_id" INTEGER,
      "product_name" VARCHAR(5),
      "price" INTEGER
    );
    
    INSERT INTO menu
      ("product_id", "product_name", "price")
    VALUES
      ('1', 'sushi', '10'),
      ('2', 'curry', '15'),
      ('3', 'ramen', '12');
      
    
    CREATE TABLE members (
      "customer_id" VARCHAR(1),
      "join_date" DATE
    );
    
    INSERT INTO members
      ("customer_id", "join_date")
    VALUES
      ('A', '2021-01-07'),
      ('B', '2021-01-09');

---

**Query #1**

    SELECT
      	s.customer_id, sum(m.price)
    FROM dannys_diner.sales as s
    inner join dannys_diner.menu as m
    on s.product_id=m.product_id
    group by s.customer_id
    order by s.customer_id;

| customer_id | sum |
| ----------- | --- |
| A           | 76  |
| B           | 74  |
| C           | 36  |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)
