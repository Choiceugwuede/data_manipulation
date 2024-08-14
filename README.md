# Customer Behavior Analysis for a Restaurant: A SQL Case Study

## Overview

This project focuses on analyzing customer behavior and preferences for a restaurant using SQL. The goal is to leverage data to answer key questions that can help inform business decisions, optimize customer experience, and enhance overall operational efficiency.

In this case study, I explore various aspects of the restaurant's data, including purchasing power, customer preferences, and behavior patterns. By analyzing this data, the project aims to provide actionable insights that the restaurant can use to devise personalized customer experiences and improve its service offerings.

## Skills demonstrated

- SQL Proficiency
- Data Manipulation

## Tools

- SQL Flavour : PostgreSQL v13

## Data 

- Sales table: captures all customer_id level purchases with an corresponding order_date and product_id information for when and what menu items were ordered.
  
- Menu table: maps the product_id to the actual product_name and price of each menu item.
  
- Mmebers table: captures the join_date when a customer_id joined resaturant loyalty program.

## Case study 

1. What is the total amount each customer spent at the restaurant?
  ```sql
SELECT s.customer_id,SUM(m.price) as total_amount_spent
FROM dannys_diner.sales s 
JOIN dannys_diner.menu m
ON s.product_id=m.product_id
GROUP BY s.customer_id;
```
2. How many days has each customer visited the restaurant?
  ```sql
SELECT customer_id,COUNT(order_date) as num_of_days_visited
FROM dannys_diner.sales
GROUP BY customer_id;
```
3. What was the first item from the menu purchased by each customer?
 ```sql
SELECT customer_id,product_name
FROM dannys_diner.sales s 
JOIN dannys_diner.menu m
ON s.product_id=m.product_id
WHERE s.order_date in (
     SELECT MIN(order_date)
     FROM dannys_diner.sales
     GROUP BY customer_id
)
GROUP BY s.customer_id,m.product_name;
   ```
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql
SELECT m.product_name, COUNT(s.product_id) num_of_times_purchased
FROM dannys_diner.menu m
JOIN dannys_diner.sales s
ON m.product_id=s.product_id
GROUP BY m.product_name
ORDER BY num_of_times_purchased DESC limit 1;
```
5. Which item was the most popular for each customer?
```sql
SELECT m.product_name, count(s.product_id) num_of_times_purchased
FROM dannys_diner.menu m
JOIN dannys_diner.sales s
ON m.product_id=s.product_id
GROUP BY m.product_name
ORDER BY num_of_times_purchased limit 1;
```
6. Which item was purchased first by the customer after they became a member?
```sql
SELECT s.customer_id, m.product_name
FROM dannys_diner.sales s
JOIN dannys_diner.menu m ON s.product_id = m.product_id
JOIN dannys_diner.members n ON s.customer_id = n.customer_id
WHERE s.order_date = (
    SELECT MIN(order_date)
    FROM dannys_diner.sales s2
    WHERE s2.customer_id = s.customer_id
    AND s2.order_date >= n.join_date
)
GROUP BY s.customer_id, m.product_name;
```
7. Which item was purchased just before the customer became a member?
```sql
SELECT s.customer_id, m.product_name
FROM dannys_diner.sales s
JOIN dannys_diner.menu m ON s.product_id = m.product_id
JOIN dannys_diner.members n ON s.customer_id = n.customer_id
WHERE s.order_date = (
    SELECT MAX(order_date)
    FROM dannys_diner.sales s2
    WHERE s2.customer_id = s.customer_id
    AND s2.order_date <= n.join_date
)
GROUP BY s.customer_id, m.product_name;
```
8. What is the total items and amount spent for each member before they became a member?
```sql
SELECT s.customer_id,
       COUNT(s.product_id) AS total_items,
       SUM(m.price) AS total_amount_spent
FROM dannys_diner.sales s
JOIN dannys_diner.menu m ON s.product_id = m.product_id
JOIN dannys_diner.members n ON s.customer_id = n.customer_id
WHERE s.order_date < n.join_date
GROUP BY s.customer_id;
```
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql
SELECT 
    s.customer_id, 
    SUM(CASE 
        WHEN m.product_name = 'sushi' THEN m.price * 20 
        ELSE m.price * 10 
    END) total_points
FROM dannys_diner.sales s
JOIN dannys_diner.menu m ON s.product_id = m.product_id
GROUP BY s.customer_id;
```
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
```sql



