# Capstone_1
Iowa liquor sales SWOT analysis 

-- This project analyzes the Iowa Liquor Sales database with a focus on the vendor Sazerac. Using SQL in pgAdmin, the project answers SWOT-style -- business questions about sales performance, product patterns, store activity, and revenue trends.

/* Name: Mina Saeday
Vendor of Choice: Sazerac
Project: Iowa Liquor Database SWOT Analysis
*/
SELECT DISTINCT vendor_name
FROM products
WHERE vendor_name ILIKE '%sazerac%'


SELECT *
FROM public.sales
LIMIT 10;


SELECT
	s.*,
	p.vendor_name,
	p.item_description
FROM public.sales s
JOIN public.products p
ON p.item_no = s.item
WHERE p.vendor_name ILIKE '%sazerac%'
LIMIT 10

-- CASE Statement
SELECT
    SUM(s.total) AS total_sales,
    CASE
        WHEN p.vendor_name ILIKE '%sazerac%' THEN 'Sazerac'
    END AS new_vendor_name
FROM public.products p
JOIN public.sales s
    ON p.item_no = s.item
WHERE p.vendor_name ILIKE '%sazerac%'
GROUP BY new_vendor_name;


-- Name: Mina Saeday, Category/Vendor of Choice: Sazerac


-- SELECT, Filtering & Sorting
-- 1. Create a list of all transactions for your chosen [Category/Vendor] that took place in 
-- the last quarter of 2014, sorted by the total sale amount from highest to lowest. 
-- (Strength: Identifying high-volume peak periods).
SELECT s.date, s.total, s.store, p.item_description, p.vendor_name
FROM public.sales s
JOIN public.products p
	ON p.item_no = s.item
WHERE p.vendor_name ILIKE '%sazerac%'
	AND s.date BETWEEN '2014-10-01' AND '2014-12-31'
ORDER BY s.total DESC

-- 2. Which transactions for your [Category/Vendor] had a bottle quantity greater than 12?
-- -- Display the date, store number, item description, and total amount.
(Strength: Identifying bulk buyers or wholesale-style transactions).

SELECT date, store, description, total
FROM public.sales 
WHERE vendor ILIKE '%sazerac%'
	AND bottle_qty > 12

-- 3. Find all products in the products
table whose item
_
_
description contains a specific
-- keyword (e.g.,
'Limited','Spiced'). What categories do they belong to?
-- (Opportunity: Identifying niche product variants).

SELECT DISTINCT item_description
FROM public.products
WHERE vendor_name ILIKE '%sazerac%' 
ORDER BY item_description;        -- just checking what keywords exist with my specific vendor


SELECT DISTINCT item_description, category_name
FROM public.products
WHERE vendor_name ILIKE '%sazerac%'
 AND item_description ILIKE '%Spicebox%'
 
-- Aggregation
-- 4. What is the total sales revenue and the average bottle price (btl
__price) for your chosen [Category/Vendor]? (Strength/Baseline: Establishing the financial footprint).

SELECT SUM(total :: numeric) AS total_sales_revenue, AVG(btl_price :: numeric ) AS average_bottle_price -- btl_price was stored as a money data type so I cast it to numeric
FROM public.sales
WHERE vendor ILIKE '%sazerac%'
 -- result  37321641.83	 and 12.3367978852909464
 



