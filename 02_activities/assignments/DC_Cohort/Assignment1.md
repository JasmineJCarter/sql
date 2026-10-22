# DC Assignment 1: Meet the farmersmarket.db and Basic SQL

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

#### Submission Parameters:
* Submission Due Date: `March 31, 2026`
* Weight: 30% of total grade
* The branch name for your repo should be: `assignment-one`
* What to submit for this assignment:
    * This markdown (Assignment1.md) with written responses in Section 4
    * One Entity-Relationship Diagram (preferably in a pdf, jpeg, png format).
    * One .sql file 
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sql/pulls/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `assignment-one`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.

*** 

## Section 1:
You can start this section following *session 1*.

Steps to complete this part of the assignment:
- Load the farmersmarket.db and browse its content
- Create a logical data model

<br>
If this is your first time in DB Browser for SQLite, the following instructions may help:

#### 1) Load Database
- Open DB Browser for SQLite
- Go to File > Open Database
- Navigate to your farmersmarket.db 
	- This will be wherever you cloned the GH Repo (within the **05_src/sql** folder)
	- ![db_browser_for_sqlite_choose_db.png](./images/01_db_browser_for_sqlite_choose_db.png)

#### 2) Configure your windows
By default, DB Browser for SQLite has three windows, with four tabs in the main window and three tabs in the bottom right window
- Window 1: Main Window (Centre)
	- Stay in the Database Structure tab for now
- Window 2: Edit Database Cell (Top Right)
- Window 3: Remote (Bottom Right)
	- Switch this to DB Schema tab (very bottom)

Your screen should look like this (or very similar)
![db_browser_for_sqlite.png](./images/01_db_browser_for_sqlite.png)

#### 3) The farmersmarket.db
There are 10 tables in the Main Window:
1) booth
2) customer
3) customer_purchases
4) market_date_info
5) product
6) product_category
7) vendor
8) vendor_booth_assignments
9) vendor_inventory
10) postal_data

Switch to the Browse Data tab, booth is selected by default

<img src="./images/01_the_browse_data_tab.png" width="900">


Using the table drop down at the top left, explore some of the contents of the database

<img src="./images/01_the_table_drop_down_at_the_top_left.png" width="200">

Move on to the Logical Data Model task when you have looked through the tables


### Build Logical Data Model

Recall during session 1:

I diagramed the following four tables:
- product
- product_category
- vendor
- vendor_inventory

 <img src="./images/01_farmers_market_logical_model_partial.png" width="500">


#### Prompt 1:
Choose two tables and create a logical data model. There are lots of tools you can do this (including drawing this by hand), but I'd recommend [Draw.io](https://www.drawio.com/) or [LucidChart](https://www.lucidchart.com/pages/). 

A logical data model must contain:
- table name
- column names
- relationship type

Please do not pick the exact same tables that I have already diagrammed. For example, you shouldn't diagram the relationship between `product` and `product_category`, but you could diagram `product` and `customer_purchases`.

**HINTS**:
- You will need to use the Browse Data tab in the main window to figure out the relationship types.
- You can't diagram tables that don't share a common column
	- These are the tables that are connected
	- <img src="./images/01_farmers_market_conceptual_model.png" width="600">
- The column names can be found in a few spots (DB Schema window in the bottom right, the Database Structure tab in the main window by expanding each table entry, at the top of the Browse Data tab in the main window)

#### Logical Data Model: `customer` and `customer_purchases`

```mermaid
erDiagram
    customer ||--o{ customer_purchases : makes

    customer {
        int customer_id PK
        nvarchar customer_first_name
        nvarchar customer_last_name
        char customer_postal_code
    }

    customer_purchases {
        int product_id PK
        int vendor_id PK
        date market_date PK
        int customer_id PK, FK
        decimal quantity
        decimal cost_per_quantity
        time transaction_time PK
    }
```

One customer can make zero or many purchases; each purchase is associated with one customer through `customer_id`.

***

## Section 2:
You can start this section following *session 2*.

Steps to complete this part of the assignment:
- Completed SQL responses are included below each question.
- A standalone copy of the SQL responses is also available in [assignment1.sql](./assignment1.sql).

### Write SQL

#### SELECT
1. Write a query that returns everything in the customer table.

```sql
SELECT *
FROM customer;
```

2. Write a query that displays all of the columns and 10 rows from the customer table, sorted by customer_last_name, then customer_first_ name.

```sql
SELECT *
FROM customer
ORDER BY customer_last_name, customer_first_name
LIMIT 10;
```

<div align="center">-</div>

#### WHERE
1. Write a query that returns all customer purchases of product IDs 4 and 9. Limit to 25 rows of output.

```sql
SELECT *
FROM customer_purchases
WHERE product_id IN (4, 9)
LIMIT 25;
```

2. Write a query that returns all customer purchases and a new calculated column 'price' (quantity * cost_per_quantity), filtered by customer IDs between 8 and 10 (inclusive) using either:
	1.  two conditions using AND
	2.  one condition using BETWEEN <br>
   Limit to 25 rows of output.

```sql
SELECT *,
       quantity * cost_per_quantity AS price
FROM customer_purchases
WHERE customer_id BETWEEN 8 AND 10
LIMIT 25;
```

<div align="center">-</div>

#### CASE
1. Products can be sold by the individual unit or by bulk measures like lbs. or oz. Using the product table, write a query that outputs the `product_id` and `product_name` columns and add a column called `prod_qty_type_condensed` that displays the word “unit” if the `product_qty_type` is “unit,” and otherwise displays the word “bulk.”

```sql
SELECT product_id,
       product_name,
       CASE
           WHEN product_qty_type = 'unit' THEN 'unit'
           ELSE 'bulk'
       END AS prod_qty_type_condensed
FROM product;
```

2. We want to flag all of the different types of pepper products that are sold at the market. Add a column to the previous query called `pepper_flag` that outputs a 1 if the product_name contains the word “pepper” (regardless of capitalization), and otherwise outputs 0.

```sql
SELECT product_id,
       product_name,
       CASE
           WHEN product_qty_type = 'unit' THEN 'unit'
           ELSE 'bulk'
       END AS prod_qty_type_condensed,
       CASE
           WHEN LOWER(product_name) LIKE '%pepper%' THEN 1
           ELSE 0
       END AS pepper_flag
FROM product;
```

<div align="center">-</div>

#### JOIN
1. Write a query that `INNER JOIN`s the `vendor` table to the `vendor_booth_assignments` table on the `vendor_id` field they both have in common, and sorts the result by `market_date` then `vendor_name`. Limit to 24 rows of output. 

```sql
SELECT vendor.*,
       vendor_booth_assignments.booth_number,
       vendor_booth_assignments.market_date
FROM vendor
INNER JOIN vendor_booth_assignments
    ON vendor.vendor_id = vendor_booth_assignments.vendor_id
ORDER BY vendor_booth_assignments.market_date, vendor.vendor_name
LIMIT 24;
```

***

## Section 3:
You can start this section following *session 3*.

Steps to complete this part of the assignment:
- Completed SQL responses are included below each question.
- A standalone copy of the SQL responses is also available in [assignment1.sql](./assignment1.sql).

### Write SQL

#### AGGREGATE
1. Write a query that determines how many times each vendor has rented a booth at the farmer’s market by counting the vendor booth assignments per `vendor_id`.

```sql
SELECT vendor_id,
       COUNT(*) AS booth_rental_count
FROM vendor_booth_assignments
GROUP BY vendor_id;
```

2. The Farmer’s Market Customer Appreciation Committee wants to give a bumper sticker to everyone who has ever spent more than $2000 at the market. Write a query that generates a list of customers for them to give stickers to, sorted by last name, then first name.
   
**HINT**: This query requires you to join two tables, use an aggregate function, and use the HAVING keyword.

```sql
SELECT customer.customer_id,
       customer.customer_first_name,
       customer.customer_last_name,
       SUM(customer_purchases.quantity * customer_purchases.cost_per_quantity) AS total_spent
FROM customer
INNER JOIN customer_purchases
    ON customer.customer_id = customer_purchases.customer_id
GROUP BY customer.customer_id,
         customer.customer_first_name,
         customer.customer_last_name
HAVING SUM(customer_purchases.quantity * customer_purchases.cost_per_quantity) > 2000
ORDER BY customer.customer_last_name, customer.customer_first_name;
```

<div align="center">-</div>

#### Temp Table
1. Insert the original vendor table into a temp.new_vendor and then add a 10th vendor: Thomass Superfood Store, a Fresh Focused store, owned by Thomas Rosenthal
   
**HINT**: This is two total queries -- first create the table from the original, then insert the new 10th vendor. When inserting the new vendor, you need to appropriately align the columns to be inserted (there are five columns to be inserted, I've given you the details, but not the syntax)

To insert the new row use VALUES, specifying the value you want for each column:  
`VALUES(col1,col2,col3,col4,col5)`

```sql
CREATE TEMP TABLE new_vendor AS
SELECT *
FROM vendor;

INSERT INTO temp.new_vendor (
    vendor_id,
    vendor_name,
    vendor_type,
    vendor_owner_first_name,
    vendor_owner_last_name
)
VALUES (10, 'Thomass Superfood Store', 'Fresh Focused', 'Thomas', 'Rosenthal');
```

<div align="center">-</div>

#### Date
1. Get the customer_id, month, and year (in separate columns) of every purchase in the customer_purchases table.
   
**HINT**: you might need to search for strfrtime modifers sqlite on the web to know what the modifers for month and year are!

Limit to 25 rows of output. 

```sql
SELECT customer_id,
       STRFTIME('%m', market_date) AS month,
       STRFTIME('%Y', market_date) AS year
FROM customer_purchases
LIMIT 25;
```

2. Using the previous query as a base, determine how much money each customer spent in April 2022. Remember that money spent is `quantity*cost_per_quantity`.
   
**HINTS**: you will need to AGGREGATE, GROUP BY, and filter...but remember, STRFTIME returns a STRING for your WHERE statement...
AND be sure you remove the LIMIT from the previous query before aggregating!! 

```sql
SELECT customer_id,
       SUM(quantity * cost_per_quantity) AS total_spent
FROM customer_purchases
WHERE STRFTIME('%m', market_date) = '04'
  AND STRFTIME('%Y', market_date) = '2022'
GROUP BY customer_id;
```

*** 

## Section 4:
You can start this section anytime.

Steps to complete this part of the assignment:
- Read the article
- Write, within this markdown file, between 250 and 1000 words. No additional citations/sources are required.

### Ethics

Read: Qadri, R. (2021, November 11). _When Databases Get to Define Family._  Wired. <br>
    https://www.wired.com/story/pakistan-digital-database-family-design/

Link if you encounter a paywall: https://archive.is/srKHV or https://web.archive.org/web/20240422105834/https://www.wired.com/story/pakistan-digital-database-family-design/

**What values systems are embedded in databases and data systems you encounter in your day-to-day life?**

Consider, for example, concepts of fariness, inequality, social structures, marginalization, intersection of technology and society, etc.


```



The article demonstrates the power of variables in databases and their validity. When database categories are too narrow, this can cause major errors. In this case, Riz lost access to basic services in Pakistan because his mother avoided taking on her husband’s family name. This shows how important it is to have valid structures within databases. Powerful database assumptions, such as about family and surname relations, can reflect social values and have the potential to marginalize people whose families don’t fit the database assumptions. The article also shows the importance of having researchers and trained professionals available to validate models to ensure their accuracy and validity, as well as their reliability for accessibility and accountability. The variables, such as gender identities, can be expanded to support inclusion. This is something we see continuously in our modern society as we adapt to and adjust to the world. Updating schemas in databases can be an easy fix once the required updates become apparent.

Fortunately, Riz was able to convince the National Database and Registration Authority (NADRA) and its database of his parents’ partnership by digging up old certificates. Riz was lucky to be able to validate his identity. He reflects on the experience as a demonstration of computation troubles of the world. Studying database logistics and relational diagram models is essential for underlying governance of countries and their citizens. Large-scale data management systems are usually relational databases and these relational ‘variables’ are encountered in our day-to-day life. We can make databases from many day-to-day encounters and it’s quite interesting to study relationships and their validity. This underlies a lot of scientific research and is such an important tool that all researchers should understand. Overall, it is important that database designers adapt to the shifting concepts of identity and the politics of ID databases. Relationships between variables are a very important aspect of databases that show a prime example of the intersection between technology and society.






```
