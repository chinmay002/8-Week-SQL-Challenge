![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/aa9ab174-c5ed-4f7d-ac43-de63a5de9f43)
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
### steps :
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


**5. Which item was the most popular for each customer?**
~~~~sql
with cte2 as(
select customer_id,product_name,
rank() over (partition by customer_id order by count desc) as enk
from (
select customer_id,product_name,count(s.product_id )
from dannys_diner.sales s
join dannys_diner.menu m on 
s.product_id = m.product_id
group by 1,2
order by 1) cte
)

select cte2.customer_id,cte2.product_name from cte2
​
~~~~
### STeps:
- Creates a **CTE** named `cte2` using a nested query to calculate the rank of each product for each customer.
- The nested query counts the occurrences of each product for each customer in **dannys_diner.sales** and **dannys_diner.menu**, then groups the results and orders them by **customer_id**.
- The outer query applies the **RANK()** window function over the partition of each **customer_id**, ordering by the count in descending order.
- Selects **customer_id** and **product_name** from the final results in **cte2**.

![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/446344f5-1440-4800-b564-3e020b95a224)

### Ramen Seems to be favorite dish for all customers.


**6. Which item was purchased first by the customer after they became a member?**

~~~~sql
with cte as (
select customer_id,me.product_name,rank() over(partition by customer_id order by order_date) rnk
from 
(select
 s.* from dannys_diner.sales s 
join dannys_diner.members m
on s.customer_id  = m.customer_id
where order_date >join_date
order by 1,2) sub
join dannys_diner.menu me
on sub.product_id = me.product_id
)
select customer_id,product_name from cte where rnk=1
~~~~

### Steps:
- Creates a **CTE** named `cte` using a nested query to calculate the rank of each product for each customer based on the order date.
- The nested query joins **dannys_diner.sales** and **dannys_diner.members** on **customer_id** and filters records where the **order_date** is greater than the **join_date**.
- The outer query applies the **RANK()** window function over the partition of each **customer_id**, ordering by **order_date**.
- Joins the results with **dannys_diner.menu** to retrieve **product_name**.
- Selects **customer_id** and **product_name** from the final results in **cte** where the rank is equal to 1.

![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/456f2f77-eebd-4fc4-a1c6-bd4f26e38d51)


**7. Which item was purchased just before the customer became a member?**

~~~~Sql
with cte as (
select customer_id,product_id,order_date,join_date , rank() over(partition by customer_id order by diff asc) 
from (
select  s.*,m.join_date ,(join_date - order_date) as diff 
  from dannys_diner.sales s 
   join dannys_diner.members m
   on s.customer_id  = m.customer_id
   and join_date>order_date )ct
)
select customer_id,order_date,join_date,product_name 
from cte 
join dannys_diner.menu m 
on cte.product_id = m.product_id
where rank = 1
~~~~

### Steps:
- Creates a **CTE** named `cte` using a nested query to calculate the rank of each product for each customer based on the ascending difference between **join_date** and **order_date**.
- The nested query joins **dannys_diner.sales** and **dannys_diner.members** on **customer_id**, filtering records where **join_date** is greater than **order_date**.
- The outer query applies the **RANK()** window function over the partition of each **customer_id**, ordering by the calculated difference (`diff`) in ascending order.
- Selects **customer_id**, **order_date**, **join_date**, and **product_id** from the final results in **cte**.
- Joins the results with **dannys_diner.menu** to retrieve **product_name**.
- Filters the final results to include only rows where the rank is equal to 1.

  ![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/ffeb9639-1267-46c5-927a-640332cf2246)

  ### Maybe Sushi was the deciding dish for customer to take up the membership


**8. What is the total items and amount spent for each member before they became a member?**
~~~~Sql
select  s.customer_id,count(product_name),sum(price) from dannys_diner.sales s 
join dannys_diner.members m
on s.customer_id  = m.customer_id
and order_date <join_date
join dannys_diner.menu 
on s.product_id = menu.product_id
group by 1
~~~~

![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/1b292aa0-f2a4-43da-b438-ac76e403e5df)


**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier — how many points would each customer have?**

~~~~Sql
with cte as (
select s.customer_id,m.product_name,price,
case
	when product_name = 'sushi' then price*20
    else price*10
    end updated_price
from dannys_diner.sales s 
join dannys_diner.menu m
on s.product_id  = m.product_id
order by 1

)
select customer_id,sum(updated_price) as total
from cte
group by 1

~~~~

![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/191af38d-d937-49a1-a42e-319cf814d410)


# BONUS QUESTIONS

**Join All The Things**

**Recreate the table with: customer_id, order_date, product_name, price, member (Y/N)**

~~~~Sql
with cte as (
select s.customer_id,s.order_date,m.product_name,m.price,mem.join_date 
from dannys_diner.sales  s
left join dannys_diner.menu m
on s.product_id = m.product_id
left join dannys_diner.members mem
on s.customer_id = mem.customer_id
)

select *,
case 
	when join_date<=order_date then 'Y'
	else 'N'
    end member_yet
from cte
~~~~

![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/cecdfc6e-1de4-4ad7-bcba-2dd7c91a876e)


**Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.**

~~~~Sql
with cte as (
select s.customer_id,s.order_date,m.product_name,m.price ,

case 
	when mem.join_date<=s.order_date then 'Y'
	else 'N'
    end member_yet
from dannys_diner.sales  s
left join dannys_diner.menu m
on s.product_id = m.product_id
left join dannys_diner.members mem
on s.customer_id = mem.customer_id
)

select *,
case
	when member_yet='Y' then dense_rank() over(partition by customer_id,member_yet order by order_date)
	else Null
	end rnk_purchased_members
from cte
~~~~

![image](https://github.com/chinmay002/8-Week-SQL-Challenge/assets/60249099/50b2e4fc-09ce-4019-a9c1-c03957aa69bb)

