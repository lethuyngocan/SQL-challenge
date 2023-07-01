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

**Query #1**

    SELECT
      	s.customer_id, sum(m.price)
    FROM dannys_diner.sales as s
    INNER JOIN dannys_diner.menu as m
    ON s.product_id=m.product_id
    GROUP BY s.customer_id
    ORDER BY s.customer_id;

| customer_id | sum |
| ----------- | --- |
| A           | 76  |
| B           | 74  |
| C           | 36  |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)**Query #1**

2) How many days has each customer visited the restaurant?

**Query #2**

    SELECT customer_id, COUNT(DISTINCT(order_date))
    FROM dannys_diner.sales
    GROUP BY customer_id
    ORDER BY customer_id;

| customer_id | count |
| ----------- | ----- |
| A           | 4     |
| B           | 6     |
| C           | 2     |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138) 

3) What was the first item from the menu purchased by each customer?

**Query #3**

    SELECT customer_id, product_name
    FROM (
      SELECT s.customer_id, 
             m.product_name,
             s.order_date,
             MIN(s.order_date) OVER (PARTITION BY s.customer_id) AS min_order_date
      FROM dannys_diner.sales AS s
      INNER JOIN dannys_diner.menu AS m ON s.product_id = m.product_id
    ) AS subquery
    WHERE order_date = min_order_date;

| customer_id | product_name |
| ----------- | ------------ |
| A           | sushi        |
| A           | curry        |
| B           | curry        |
| C           | ramen        |
| C           | ramen        |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)

4) What is the most purchased item on the menu and how many times was it purchased by all customers?

**Query #4**

    SELECT m.product_name, COUNT(s.product_id) as purchased_time
    FROM dannys_diner.sales as s
    INNER JOIN dannys_diner.menu as m
    ON s.product_id= m.product_id
    GROUP BY product_name
    LIMIT 1;

| product_name | purchased_time |
| ------------ | -------------- |
| ramen        | 8              |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)

5) Which item was the most popular for each customer?

**Query #5**

    WITH cte_productname AS(
        SELECT
            s.customer_id,
            s.product_id,
         	m.product_name,
            COUNT(s.product_id) AS frq
        FROM
            dannys_diner.sales AS s
        INNER JOIN
            dannys_diner.menu AS m ON s.product_id = m.product_id
        GROUP BY
            s.customer_id,
            s.product_id,
            m.product_name
        ORDER BY
            s.customer_id,
            frq DESC
        )
        SELECT 
        	customer_id,
            product_name,
            max_count
        FROM (
        SELECT
        	customer_id,
            product_name,
            frq,
            MAX (frq) OVER (PARTITION BY customer_id) AS max_count
        FROM
        	cte_productname
           ) AS subquery 
        WHERE max_count=frq;

| customer_id | product_name | max_count |
| ----------- | ------------ | --------- |
| A           | ramen        | 3         |
| B           | ramen        | 2         |
| B           | sushi        | 2         |
| B           | curry        | 2         |
| C           | ramen        | 3         |

---
* Customer A and C purchased ramen the most frequent
* Customer B purchased all kinds of food in the restaurant


Take away: To sovling this question I combined different SQL advancaed techniques such as:
* CTE function
* Window Function
* Subquery
* Join Table
* Group by

6) Which item was purchased first by the customer after they became a member?

**Query #6**

    WITH cte_joindate AS (
    SELECT
    	s.customer_id,
        s.order_date,
        m.product_name
    FROM 
    	dannys_diner.sales as s
    INNER JOIN
    	dannys_diner.menu as m
    ON s.product_id=m.product_id
    
    INNER JOIN
    	dannys_diner.members as me
    ON s.customer_id=me.customer_id
    WHERE s.order_date>=me.join_date
    ORDER BY customer_id, order_date
    )
    SELECT
    	customer_id,
        order_date,
        product_name,
        rank_date
    FROM
    (
    SELECT
    	customer_id,
        order_date,
        product_name,
        RANK() OVER (PARTITION BY customer_id
                        ORDER BY order_date)
                  AS rank_date
    FROM cte_joindate)
    AS subquery
    WHERE rank_date=1;

| customer_id | order_date               | product_name | rank_date |
| ----------- | ------------------------ | ------------ | --------- |
| A           | 2021-01-07T00:00:00.000Z | curry        | 1         |
| B           | 2021-01-11T00:00:00.000Z | sushi        | 1         |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)

Customer A orders curry after he/she became restaurant loyal member while Customer B orders sushi

7)Which item was purchased just before the customer became a member?

**Query #7**

    WITH CTE_bfmember AS (
        SELECT
            s.customer_id,
            s.order_date,
            m.product_name,
            RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS rank_date
        FROM
            dannys_diner.sales AS s
        INNER JOIN
            dannys_diner.menu AS m ON s.product_id = m.product_id
        INNER JOIN
            dannys_diner.members AS me ON s.customer_id = me.customer_id
        WHERE
            s.order_date < me.join_date
        ORDER BY
            s.customer_id,
            s.order_date DESC
    )
    SELECT 
    	customer_id,
        product_name,
        order_date
    FROM
    (
    SELECT
        customer_id,
        product_name,
        order_date,
        rank_date,
        MAX(rank_date) OVER (PARTITION BY customer_id) AS beforemember_date
    FROM
        CTE_bfmember
     ) as subquery
     WHERE
     rank_date= beforemember_date;

| customer_id | product_name | order_date               |
| ----------- | ------------ | ------------------------ |
| A           | sushi        | 2021-01-01T00:00:00.000Z |
| A           | curry        | 2021-01-01T00:00:00.000Z |
| B           | sushi        | 2021-01-04T00:00:00.000Z |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)

* Customer A ordered sushi and curry on 2021/01/01 right before became a member
* Customer B ordered sushi on 2021/01/ 04 right before became a member

8) What is the total items and amount spent for each member before they became a member?
   
**Query #8**

    WITH CTE_product AS
    (
       SELECT
        s.customer_id,
        s.order_date,
        m.product_name,
        m.price
    FROM
        dannys_diner.sales AS s
    INNER JOIN
        dannys_diner.menu AS m ON s.product_id = m.product_id
    INNER JOIN
        dannys_diner.members AS me ON s.customer_id = me.customer_id
    WHERE
        s.order_date < me.join_date
    ORDER BY
        s.customer_id,
        s.order_date DESC
    )
    SELECT
        customer_id,
        COUNT(DISTINCT product_name) AS total_item,
        SUM(price) AS total_price
    FROM    
    	CTE_product
    GROUP BY 
    	customer_id;

| customer_id | total_item | total_price |
| ----------- | ---------- | ----------- |
| A           | 2          | 25          |
| B           | 2          | 40          |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)
