## Case Study Questions


### --1. What is the total amount each customer spent at the restaurant?
<br>

````sql

    SELECT s.customer_id, SUM(m.product_id) AS Total_Spent
    
    FROM dannys_diner.sales AS s
    JOIN dannys_diner.menu AS m
    	ON s.product_id = m.product_id 
        
    GROUP BY customer_id
    ORDER BY customer_id;
````
SUM the ```product_id``` to get the total spent
<br>
GROUP BY ```customer_id``` to find out how much each customer is spending
<br>
JOIN both ```menu``` and ```sales``` tables using ```product_id``` 

| customer_id | total_spent |
| ----------- | ----------- |
| A           | 14          |
| B           | 12          |
| C           | 9           |


---


### -- 2. How many days has each customer visited the restaurant?
<br>

````sql
    SELECT customer_id, COUNT(DISTINCT (order_date)) AS Visit_Count
    FROM dannys_diner.sales
        
    GROUP BY customer_id
    ORDER BY customer_id;
````
DISTINCT and COUNT to find out the ```Visit_Count``` for each customer 
<br>

| customer_id | visit_count |
| ----------- | ----------- |
| A           | 4           |
| B           | 6           |
| C           | 2           |



---



### -- 3. What was the first item from the menu purchased by each customer?
<br>


````sql
    WITH cte_first_item AS (
      SELECT s.customer_id, s.order_date, m.product_name,
      DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS ranking
      
      FROM dannys_diner.sales s
      JOIN dannys_diner.menu m
      	ON s.product_id=m.product_id
      )
      
    SELECT customer_id, product_name
    FROM cte_first_item 
    WHERE ranking = 1
    GROUP BY customer_id, product_name;
````

Create a cte, ```cte_first_item``` then use windows function to partition ```customer_id``` by ```order_date```
<br> 
WHERE ```ranking``` = 1 means to show only the first item the customers ordered

| customer_id | product_name |
| ----------- | ------------ |
| A           | curry        |
| A           | sushi        |
| B           | curry        |
| C           | ramen        |

---


### -- 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
<br>

Finding out which item is the most purchased on the menu
````SQL
    WITH most_purchased_cte AS (
      SELECT product_name, rank()OVER(ORDER BY COUNT(product_name)DESC) AS row_rank
      
      FROM dannys_diner.sales s
      JOIN dannys_diner.menu m 
      	ON s.product_id = m.product_id
      GROUP BY product_name
      )
    
    
    SELECT * 
    FROM most_purchased_cte 
    WHERE row_rank = 1;
````

Create a cte and COUNT() number of ```product_name``` 

| product_name | row_rank |
| ------------ | -------- |
| ramen        | 1        |

---

Finding out how many times was it purchased by all the customers

````SQL

    WITH most_purchased_cte AS (
      SELECT product_name, COUNT(product_name)AS count_of_item
      
      FROM dannys_diner.sales s
      JOIN dannys_diner.menu m 
      	ON s.product_id = m.product_id
      GROUP BY product_name
      )
    
    
    SELECT * 
    FROM most_purchased_cte 
    ORDER BY count_of_item DESC;
````

COUNT() the number of ```product_name``` and call the cte and ORDER BY in DESC order

| product_name | count_of_item |
| ------------ | ------------- |
| ramen        | 8             |
| curry        | 4             |
| sushi        | 3             |

---



### -- 5. Which item was the most popular for each customer?
<br>

````SQL
    WITH most_popular AS (
      SELECT customer_id, product_name, 
      DENSE_RANK()OVER(PARTITION BY customer_id ORDER BY COUNT(m.product_id)) AS most_popular_item
      
      FROM dannys_diner.sales s
      JOIN dannys_diner.menu m
      	ON s.product_id=m.product_id
      GROUP BY customer_id, product_name
      )
      
    SELECT *
    FROM most_popular
    WHERE most_popular_item = 1;
````    
Arrange and COUNT by ```product_id```, then PARTITION BY ```customer_id``` 

| customer_id | product_name | most_popular_item |
| ----------- | ------------ | ----------------- |
| A           | sushi        | 1                 |
| B           | ramen        | 1                 |
| B           | curry        | 1                 |
| B           | sushi        | 1                 |
| C           | ramen        | 1                 |

---


### -- 6. Which item was purchased first by the customer after they became a member?
<br>

````SQL
    WITH cte AS (
      SELECT s.customer_id, m.product_name, 
      DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS order_dates
      
      FROM dannys_diner.sales s
      JOIN dannys_diner.menu m 
      	ON s.product_id = m.product_id 
      JOIN dannys_diner.members a 
      ON a.customer_id = s.customer_id
      WHERE a.join_date <= s.order_date
      
      )
    
    SELECT * 
    FROM cte
    WHERE order_dates = 1;
    
````
i want to rank the order dates within the ```customer_id```
then joining menu and sales table to get ```product_name```


| customer_id | product_name | order_dates |
| ----------- | ------------ | ----------- |
| A           | curry        | 1           |
| B           | sushi        | 1           |

---


### -- 7. Which item was purchased just before the customer became a member?
<br>

````SQL
    WITH cte AS (
      SELECT s.customer_id, m.product_name, 
      DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS order_dates
      
      
      FROM dannys_diner.sales s
      JOIN dannys_diner.menu m 
      	ON s.product_id = m.product_id 
      
      
      JOIN dannys_diner.members a 
      ON a.customer_id = s.customer_id
      WHERE a.join_date > s.order_date
      
      )
    
    SELECT * 
    FROM cte
    WHERE order_dates = 1;
````

| customer_id | product_name | order_dates |
| ----------- | ------------ | ----------- |
| A           | sushi        | 1           |
| A           | curry        | 1           |
| B           | curry        | 1           |

---



### -- 8. What is the total items and amount spent for each member before they became a member?
<br>

### -- 9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
<br>

### -- 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
<br>
