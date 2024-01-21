
# 🍜 Case Study #1: Danny's Diner
![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/6e51e243-b24f-457c-8202-869c46823844)


# Business Task
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite

# Entity Relationship Diagram
![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/8099fe0a-c447-435b-af59-ac10d6ccce5a)


# Question and Solution
**1. What is the total amount each customer spent at the restaurant?**

~~~~sql
SELECT
  	s.customer_id,
    sum(m.price)
FROM dannys_diner.sales s
join dannys_diner.menu m
on s.product_id = m.product_id
group by 1
order by 1
~~~~
## steps :
* **Calculates total spent per customer**: Joins sales and menu data based on product ID, then sums up prices for each customer.
* **Groups and orders by customer**: Aggregates individual purchases into total spends per customer and arranges them alphabetically.
* **Outputs customer IDs and total spend amounts**: Provides a table showing each customer's total expenditure at Danny's Diner.

## Answer
* Customer A spent $76.
* Customer B spent $74.
* Customer C spent $36.

**2. How many days has each customer visited the restaurant?**

~~~~sql
select customer_id,count(distinct order_date) as visit 
from dannys_diner.sales
group by 1
~~~~
![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/606ecac1-d696-4db2-8586-9d67b49c6528)

**3. What was the first item from the menu purchased by each customer?**
~~~~sql
with cte as 
	(select 
     customer_id,
     dense_rank() over(partition by customer_id order by order_date asc) as rnk,
     product_id  
     from dannys_diner.sales) 

select cte.customer_id, m.product_name from cte join dannys_diner.menu m
on cte.product_id = m.product_id
where cte.rnk=1
group by 1,2
~~~~
## Steps:
- A **CTE** named `cte` is defined using **dense_rank()** to assign a rank based on **order_date** within each customer's partition.
- The **CTE** selects **customer_id**, **rnk** (dense rank), and **product_id** from **dannys_diner.sales**.
- The main query retrieves **customer_id** and **product_name** by joining **cte** with **dannys_diner.menu** on **product_id**.
- The **WHERE** clause filters records to include only those with a rank of 1 (earliest order for each customer).

![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/2fade4f4-962f-4659-8852-5305b051ad87)

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
~~~~sql
SELECT 
  menu.product_name,
  COUNT(sales.product_id) AS most_purchased_item
FROM dannys_diner.sales
JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
GROUP BY menu.product_name
ORDER BY most_purchased_item DESC
LIMIT 1;
~~~~
## Steps:
- Selects **product_name** from the **dannys_diner.menu** table and counts the occurrences of each **product_id** in **dannys_diner.sales**.
- Joins **dannys_diner.sales** and **dannys_diner.menu** on **product_id**.
- Groups the results by **product_name**.
- Orders the groups by the count of **product_id** in descending order.
- Limits the result to the top result (most purchased item) using **LIMIT 1**.

![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/7df85dbc-9eda-4817-ac2b-9b10e07e0235)



