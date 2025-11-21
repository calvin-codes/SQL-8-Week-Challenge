
**1. What is the total amount each customer spent at the restaurant?**
```` sql
SELECT sales.customer_id, SUM(menu.price) AS total_amount
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id
ORDER BY total_amount DESC
````
**Solution:**\
<img width="250" height="192" alt="image" src="https://github.com/user-attachments/assets/0995fece-14c7-4c06-a52d-615d1b20aec6" />


**2. How many days has each customer visited the restaurant?**

````sql
SELECT customer_id, COUNT(DISTINCT order_date) AS visit_num
FROM sales 
GROUP BY customer_id
ORDER BY visit_num DESC
````
**Solution:**\
<img width="250" height="200" alt="image" src="https://github.com/user-attachments/assets/3a2bdf7d-b06f-45b7-9039-ecd70e1d4995" />


**3. What was the first item from the menu purchased by each customer?**
````sql
WITH first_order AS(
  SELECT sales.customer_id,  menu.product_name,
    ROW_NUMBER() OVER (
      PARTITION BY sales.customer_id
      ORDER BY sales.order_date) AS row_num
  FROM sales 
  JOIN menu ON sales.product_id = menu.product_id)

SELECT customer_id, product_name
FROM first_order
WHERE row_num = 1
````
**Solution:**\
<img width="250" height="200" alt="image" src="https://github.com/user-attachments/assets/9dfbd59c-dc15-4753-b454-cf85926a6a6e" />


**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
````sql
SELECT menu.product_name, COUNT(sales.product_id) AS purchase_num
FROM menu 
JOIN sales ON menu.product_id = sales.product_id
GROUP BY menu.product_name
ORDER BY purchase_num DESC
LIMIT 1
````
**Solution:**\
<img width="250" height="200" alt="image" src="https://github.com/user-attachments/assets/9eaee9bd-3ae0-400c-825c-0c46bab588ee" />

**5. Which item was the most popular for each customer?**
````sql
WITH popular_item AS(
SELECT sales.customer_id, menu.product_name, COUNT(sales.product_id) AS order_num,
       DENSE_RANK() OVER (
            PARTITION BY sales.customer_id 
            ORDER BY COUNT(sales.product_id) DESC)AS product_rank

FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id, menu.product_name
)
SELECT customer_id, product_name, order_num
FROM popular_item
WHERE product_rank = 1
````
**Solution:**\
<img width="250" height="280" alt="image" src="https://github.com/user-attachments/assets/f8ae381b-ea88-478f-8c4a-e9c0f0470b72" />

**6. Which item was purchased first by the customer after they became a member?**
````sql
WITH became_member AS (
SELECT members.customer_id, sales.product_id, menu.product_name,
    ROW_NUMBER() OVER(
      PARTITION BY members.customer_id
      ORDER BY sales.order_date) AS product_rank
FROM members 
JOIN sales ON members.customer_id = sales.customer_id AND members.join_date < sales.order_date
JOIN menu ON sales.product_id = menu.product_id
)
SELECT customer_id, product_name
FROM became_member
WHERE product_rank = 1
````
**Solution:**\
<img width="250" height="146" alt="image" src="https://github.com/user-attachments/assets/e2147484-2821-4c88-8288-bc3af0102fbf" />
