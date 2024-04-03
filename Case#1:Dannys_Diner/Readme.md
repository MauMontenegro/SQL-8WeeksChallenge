## Dannys Dinner
- *What is the total amount each customer spent at the restaurant?*

```sql
SELECT sales.customer_id,
SUM(menu.price) as Total 
FROM sales
JOIN
menu
ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY Total DESC;
```

We make an **INNER JOIN** between 'sales' and 'menu' tables because we need to relate the product price and how many times this product was bought. We **SUM** over menu.price and **GROUP BY** sales.customer_id to relate which and how many products has a customer bought and sum their price.

![image](https://hackmd.io/_uploads/B1fiIpSy0.png)

- How many days has each customer visited the restaurant?

```sql
SELECT customer_id,COUNT(DISTINCT order_date) AS VISITS
FROM sales 
GROUP BY customer_id;
```

We **GROUP BY** customer_id and count the distinct dates by customer.

![image](https://hackmd.io/_uploads/SynQqaHJR.png)


-  What was the first item from the menu purchased by each customer?

```sql 
WITH ordered AS(
    SELECT sales.customer_id,sales.order_date,menu.product_name,
    DENSE_RANK() OVER (PARTITION BY sales.customer_id ORDER BY sales.order_date ASC) as ranking
    FROM sales INNER JOIN menu
    ON sales.product_id = menu.product_id
)

SELECT customer_id,product_name as FIRST_ITEM
FROM ordered
WHERE ranking=1
GROUP BY customer_id,product_name; 

```

WE first create a sub table called ´ordered´ to handle a **DENSE_RANK** over the dates in which the client has bought some products. Then, we **SELECT** the item with the first ranking and **GROUP BY** customer.

![image](https://hackmd.io/_uploads/rJVv_ABJR.png)

-  Which item was the most popular for each customer?

```sql
WITH popular AS (
    SELECT sales.customer_id,menu.product_name, 
    COUNT(sales.product_id) AS order_count,
    DENSE_RANK() OVER (PARTITION BY sales.customer_id ORDER BY COUNT(sales.customer_id) DESC) AS ranked
    FROM menu
    INNER JOIN
    sales
    ON menu.product_id = sales.product_id
    GROUP BY sales.customer_id, menu.product_name
)

SELECT 
  customer_id, 
  product_name, 
  order_count
FROM popular
WHERE ranked = 1;
```

Create a CTE called *popular* where we make a "dense_rank()" over the customers ordered by the total times a product has been purchased from "Most popular" to "Less popular". Then, in the outer quey que just select the first rank for each client (If there is a draw, then all these items are shown). 

- Which item was purchased first by the customer after they became a member?

[](https://)
