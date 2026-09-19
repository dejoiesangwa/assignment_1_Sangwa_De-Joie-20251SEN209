# assignment_1_Sangwa_De-Joie-20251SEN209

# names:Sangwa De Joie
# id :20251SEN209
# group: c
## database management system used: Oracle (in terminal)
## created tables -> tables_creation.png
## inserted data in customers -> customers_data.png
## inserted data in products -> products_data.png
## inserted data in customers -> customers_data.png
## inserted data in orders -> orders_data.png
## inserted data in order_items -> order_items_data.png

## A) JOIN QUERIES
 ### (1)  (INNER JOIN: orders + customers) also shown in INNER JOIN orders and customer.png.
  SELECT C.customer_name,C.city ,o.order_date 
  from customers C 
  INNER JOIN orders o 
  on o.customer_id = C.customer_id;
  ### (2)  (JOIN: order_items + products) also shown in JOIN order_items and products.png
   select p.product_name, p.category, p.price ,o.quantity 
   FROM products p
   JOIN order_items o 
   on o.product_id = p.product_id;
   ### (3) (LEFT JOIN: customers + orders) also shown in LEFT JOIN customers.png
  select c.customer_name, o.order_id, o.order_date
  FROM customers c
  LEFT JOIN orders o 
  on c.customer_id = o.customer_id;

## B) CTE QUERIES  -> CTE query.png
### the query used is this:
  WITH customer_totals AS (
      SELECT
      c.customer_id,
      c.customer_name,
            SUM(oi.quantity * p.price) AS total_spend
        FROM customers c
        JOIN orders o
           ON c.customer_id = o.customer_id
        JOIN order_items oi
           ON o.order_id = oi.order_id
       JOIN products p
           ON oi.product_id = p.product_id
       GROUP BY
           c.customer_id,
           c.customer_name)
    SELECT
      customer_id,
       customer_name,
      total_spend
    FROM customer_totals
    WHERE total_spend > (
     SELECT AVG(total_spend)
       FROM customer_totals);
## (c) Window-function queries
   ###(1) Rank customers by total amount spent, highest first. also shown in customer's ranking.png
   WITH customer_totals AS (
    SELECT
        c.customer_id,
        c.customer_name,
        SUM(oi.quantity * p.price) AS total_spent
    FROM customers c
    JOIN orders o
        ON c.customer_id = o.customer_id
    JOIN order_items oi
        ON o.order_id = oi.order_id
    JOIN products p
        ON oi.product_id = p.product_id
    GROUP BY
        c.customer_id,
        c.customer_name
)
SELECT
    customer_id,
    customer_name,
    total_spent,
    RANK() OVER (ORDER BY total_spent DESC) AS spending_rank
FROM customer_totals;
###(2) Number each customer's orders in the order placed. also shown in customer's orders.png
SELECT
    c.customer_name,
    o.order_id,
    o.order_date,
    ROW_NUMBER() OVER (
        PARTITION BY o.customer_id
        ORDER BY o.order_date
    ) AS order_number
FROM orders o
JOIN customers c
    ON o.customer_id = c.customer_id;
###(3)Show a running total of revenue over time, ordered by order date. also shown in running total of revenue.png
WITH order_revenue AS (
    SELECT
        o.order_id,
        o.order_date,
        SUM(oi.quantity * p.price) AS order_total
    FROM orders o
    JOIN order_items oi
        ON o.order_id = oi.order_id
    JOIN products p
        ON oi.product_id = p.product_id
    GROUP BY
        o.order_id,
        o.order_date
)
SELECT
    order_id,
    order_date,
    order_total,
    SUM(order_total) OVER (
        ORDER BY order_date, order_id
    ) AS running_total
FROM order_revenue
ORDER BY order_date, order_id;
###(4) For each customer with more than one order, show days between the current and previous order. also shown in more than two orders customers.png.
WITH customer_orders AS (
    SELECT
        c.customer_id,
        c.customer_name,
        o.order_id,
        o.order_date,
        LAG(o.order_date) OVER (
            PARTITION BY c.customer_id
            ORDER BY o.order_date
        ) AS previous_order_date
    FROM customers c
    JOIN orders o
        ON c.customer_id = o.customer_id
)
SELECT
    customer_name,
    order_id,
    order_date,
    previous_order_date,
    order_date - previous_order_date AS days_between
FROM customer_orders
WHERE previous_order_date IS NOT NULL;


       
