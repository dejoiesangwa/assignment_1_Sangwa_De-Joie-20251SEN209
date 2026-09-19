# assignment_1_Sangwa_De-Joie-20251SEN209

# names:Sangwa De Joie
# id :20251SEN209
# group: c
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


       
