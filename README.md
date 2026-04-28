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

 
-- 5. How many transactions were recorded for each specific item description within your
-- -- chosen [Category]? Which specific product is the most frequently purchased?
(Strength: Identifying your "hero" product).

SELECT COUNT(*) AS transaction_count, description
FROM public.sales
WHERE vendor ILIKE '%sazerac%'
GROUP BY description 
ORDER BY transaction_count DESC

-- 6. Which store generated the highest total revenue for your [Category/Vendor]?
-- -- Which generated the lowest (but still greater than zero)?
(Strength vs. Weakness: Identifying your best and worst retail partners).

SELECT *
FROM public.stores
LIMIT 10;


SELECT s.store,st.name, SUM(total :: numeric) AS total_store_revenue
FROM public.sales s
JOIN public.stores ST
	ON s.store = st.store
WHERE vendor ILIKE '%sazerac%'
GROUP BY s.store, st.name
HAVING SUM (s.total::numeric) > 0
ORDER BY total_store_revenue ASC    -- lowest = 22.32

-- 7. What is the total revenue for every vendor within your chosen [Category],
-- -- sorted from highest to lowest?
(Threat: Identifying your top competitors in that space).

-- Since my assigned focus is a vendor (Sazerac) rather than a category, this question is interpreted
-- as a competitor analysis within the category space where Sazerac products appear.

SELECT DISTINCT category_name
FROM public.sales
WHERE vendor ILIKE '%sazerac%'    -- in this query I am trying to find the category space where Sazerac appears
ORDER BY category_name

-- Since Sazerac appears across many categories, I selected CANADIAN WHISKIES as the competitive category for vendor comparison
--  Question What is the total revenue for every vendor within CANADIAN WHISKIES, sorted from highest to lowest?

SELECT vendor, SUM (total::numeric) AS total_vendor_revenue
FROM public.sales                             
WHERE category_name = 'CANADIAN WHISKIES'    -- This query compares total revenue for all vendors in the CANADIAN WHISKIES
GROUP BY vendor
ORDER BY total_vendor_revenue DESC

-- 8. Which stores had total sales revenue for your [Category/Vendor] exceeding $2,000?
-- -- (Hint: Use HAVING to filter aggregated store totals).
(Strength: Pinpointing high-performing retail locations).

SELECT store, SUM(total:: numeric)::money AS total_sales_revenue
FROM public.sales
WHERE vendor ILIKE '%sazerac%'
GROUP BY store
HAVING SUM(total:: numeric) > 2000
ORDER BY total_sales_revenue ASC

-- 9. Find all sales records where the category_ name is either your chosen category or a closely related competitor category (e.g.,'VODKA 80 PROOF' vs 'IMPORTED VODKA').
(Threat: Comparing performance against direct substitutes).

-- -- Since my assigned focus is Sazerac as a vendor, this question is interpreted by comparing one Sazerac category,
-- CANADIAN WHISKIES, against a closely related competitor category, BLENDED WHISKIES.

SELECT DISTINCT category_name
FROM public.sales
WHERE category_name ILIKE '%WHISK%'
ORDER BY category_name
-- the query below Sales records for CANADIAN WHISKIES and BLENDED WHISKIES

SELECT date,store,vendor,description, category_name, total
FROM public.sales                                                     
WHERE category_name IN ('CANADIAN WHISKIES', 'BLENDED WHISKIES')
-- BELOW is Performance comparison between CANADIAN WHISKIES and BLENDED WHISKIES
SELECT 
	category_name,
	SUM(total::numeric)::money AS total_sales_revenue, 
	COUNT(*) AS transaction_count
FROM public.sales
WHERE category_name IN ('CANADIAN WHISKIES', 'BLENDED WHISKIES')
GROUP BY category_name
ORDER BY total_sales_revenue DESC


-- -- 10. List all transactions where the state bottle cost was between $10 and $20 for
-- your [Category/Vendor].
-- (Opportunity: Analyzing performance in the "mid-tier" price bracket).

SELECT date, store, description, state_btl_cost, vendor, total
FROM public.sales
WHERE vendor ILIKE '%sazerac%' 
	AND state_btl_cost::numeric BETWEEN 10 AND 20
ORDER BY state_btl_cost ASC


-- 11. Write a query that displays each store's total sales for your [Category/Vendor]
-- -- along with the store's name and address from the stores
--table.(Strength: Mapping your physical sales footprint).

SELECT s.store, st.name, st.store_address, SUM(s.total) AS total_sales_revenue
FROM public.sales s
JOIN public. stores st
	ON s.store = st.store
WHERE s.vendor ILIKE '%sazerac%' 
GROUP BY s.store, st.name, st.store_address
ORDER BY total_sales_revenue DESC


