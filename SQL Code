-- 1. Which product has the highest price? Only return a single row.

SELECT product_id
	, product_name
    , price
FROM
	products
ORDER BY
	price DESC
LIMIT 1;

-- 2. Which customer has made the most orders?

SELECT c.customer_id as Customer_id
	, concat(c.first_name, c.last_name) as customer_name
FROM
	customers c
    JOIN orders o
    ON c.customer_id = o.customer_id
GROUP BY 
	c.customer_id
ORDER BY
	COUNT(o.order_id) DESC
LIMIT 3;

-- 3. What’s the total revenue per product?

SELECT p.product_id, SUM(p.price*o.quantity) AS total_revenue
FROM
	products p
    JOIN order_items o
    ON p.product_id = o.product_id
GROUP BY
	p.product_id;
    
-- 4. Find the day with the highest revenue.

SELECT o.order_date, SUM(p.price*oi.quantity) AS highest_revenue
FROM
	order_items oi
    JOIN products p
    ON oi.product_id = p.product_id
    JOIN orders o
    ON oi.order_id = o.order_id
GROUP BY
	o.order_id , o.order_date
ORDER BY
	highest_revenue DESC
LIMIT 1;

-- 5. Find the first order (by date) for each customer.

WITH temp AS( 
	SELECT customer_id
		, order_date
		, RANK() OVER(PARTITION BY customer_id ORDER BY order_date) AS rn
	FROM 
		orders)

SELECT customer_id
	, order_date
FROM 
	temp
WHERE 
	rn = 1;

-- 6. Find the top 3 customers who have ordered the most distinct products.
WITH temp AS(
	SELECT c.first_name
        , c.last_name
        , COUNT(DISTINCT oi.product_id) as count_items
        , RANK() OVER (ORDER BY COUNT(DISTINCT oi.product_id) DESC) AS rn
	FROM
		customers c
        JOIN orders o
        on c.customer_id =o.customer_id
        JOIN order_items oi
		on o.order_id =oi.order_id
	GROUP BY
		c.first_name
        , c.last_name )
SELECT first_name
	, last_name
    , count_items
FROM
	temp
WHERE
	rn <= 3;

-- 7. Which product has been bought the least in terms of quantity?

WITH temp AS (
	SELECT p.product_name
    , SUM(oi.quantity) AS quantity_items
    , RANK() OVER (ORDER BY SUM(oi.quantity)) AS rn
    FROM
		products p
        JOIN order_items oi
        on p.product_id = oi.product_id
	GROUP BY
		p.product_name
        )
SELECT 
	product_name
	, quantity_items
FROM
	temp
WHERE 
	rn = 1;

-- 8. What is the median order total?

WITH temp AS(
	SELECT oi.order_id AS Order_ID, SUM(oi.quantity * p.price) AS Total_order
    FROM order_items oi
	JOIN products p
	ON oi.product_id = p.product_id
    GROUP BY 1
    )

SELECT ROUND(AVG(sub.tot),2) AS median
FROM ( 
    SELECT @row_index := @row_index + 1 AS row_index
		, t.Total_order as tot
    FROM 
		temp t
		, (SELECT @row_index := -1) r
    ORDER BY 
		tot 
) AS sub
WHERE 
	sub.row_index IN (FLOOR(@row_index / 2)
    , CEIL(@row_index / 2));
    
-- 9. For each order, determine if it was ‘Expensive’ (total over 300), ‘Affordable’ (total over 100), or ‘Cheap’.

SELECT oi.order_id AS Order_ID , SUM(p.price*oi.quantity) AS Order_total
	, CASE
		WHEN SUM(p.price*oi.quantity) > 300 THEN 'Expensive' 
        WHEN SUM(p.price*oi.quantity) > 100 THEN 'Affordable'
        ELSE 'Cheap'
        END AS Order_category
FROM
	order_items oi
    JOIN products p
    ON oi.product_id = p.product_id
GROUP BY oi.order_id;

/* 10. Find customers who have ordered the product with
		the highest price. */

SELECT 
	c.customer_id as customer_ID
	, concat(c.first_name, " ", c.last_name) as customer_name
FROM
	customers c
	JOIN orders o
	on c.customer_id =o.customer_id
	JOIN order_items oi
	on o.order_id =oi.order_id
	JOIN products p
	on oi.product_id = p.product_id
WHERE
	p.price = (
				SELECT MAX(price)
                FROM
					products);

/* with highest_price as (
select
	product_id,
	max(price) as max_price
from
	products
group by
	product_id
order by
	max_price desc
)
select
	c.customer_id,
	c.first_name,
	c.last_name
from
	highest_price
join products as p
		on highest_price.product_id = p.product_id
join order_items as oi
		on highest_price.product_id = oi.product_id
join orders as o
		on oi.order_id =o.order_id
join customers as c
		on o.customer_id = c.customer_id
where
	p.price = (
	select
		MAX(price)
	from
		products); */
