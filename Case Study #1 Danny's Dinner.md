/* --------------------
   Case Study Questions
   --------------------*/

### --1. What is the total amount each customer spent at the restaurant?
<br>

````sql

    SELECT s.customer_id, SUM(m.product_id) AS Total_Spent
    
    FROM dannys_diner.sales AS s
    JOIN dannys_diner.menu AS m
    	ON s.product_id = m.product_id 
        
    GROUP BY customer_id;
````
SUM the ```product_id``` to get the total spent
<br>
GROUP BY ```customer_id``` to find out how much each customer is spending
<br>
JOIN both ```menu``` and ```sales``` tables using ```product_id``` 

| customer_id | total_spent |
| ----------- | ----------- |
| B           | 12          |
| C           | 9           |
| A           | 14          |


[View on DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)

---

-- 2. How many days has each customer visited the restaurant?
<br>
-- 3. What was the first item from the menu purchased by each customer?
<br>

-- 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
<br>

-- 5. Which item was the most popular for each customer?
<br>

-- 6. Which item was purchased first by the customer after they became a member?
<br>

-- 7. Which item was purchased just before the customer became a member?
<br>

-- 8. What is the total items and amount spent for each member before they became a member?
<br>

-- 9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
<br>

-- 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
<br>
