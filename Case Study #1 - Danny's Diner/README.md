
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


