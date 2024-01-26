# 🍕 Case Study #2 - Pizza Runner



# Question and Solution
**1. How many pizzas were ordered?**

~~~~sql
SELECT COUNT(*) 
FROM customer_orders_temp;
~~~~
* Total 14 pizas were ordered

**2. How many unique customer orders were made?**
~~~~sql
SELECT 
  COUNT(DISTINCT order_id) AS unique_order_count
FROM #customer_orders;
~~~~
