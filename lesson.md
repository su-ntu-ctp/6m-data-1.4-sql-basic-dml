# Section 1: The Art of Asking (Selection, Filtering & Sorting)

### Learning Objectives

Learners will be able to retrieve specific columns, apply mathematical operators, filter datasets, and organize their results using sorting.

### Theory Recap – The "Conversation" with Data

**Narrative:** "Imagine you're looking for a specific house. First, you choose what details to look at `SELECT`. Then, you narrow it down to your favorite neighborhood `WHERE`. But finally, you want to see the cheapest ones first. That is where `ORDER BY` comes in. It doesn't change your data; it just organizes the *view* so the most important information sits right at the top."

***

### Workshop

#### Task 1: Basic Retrieval & Sorting

Open DbGate and create a new connection to the DuckDB database file `db/unit-1-4.db`.

> The table we will be using is `main.resale_flat_prices_2017`. It contains HDB's resale flat prices based on registration date from Jan-2017 onwards.  
>  
> The description for each column (a.k.a data dictionary) is as follows:  
> 
> | Title | Column Name | Data Type | Unit of Measure | Description |  
> | --- | --- | --- | --- | --- |  
> | Month | month | Datetime (Month) "YYY-MM" | - | - |  
> | Town | town | Text (General) | - | - |  
> | Flat type | flat_type | Text (General) | - | - |  
> | Block | block | Text (General) | - | - |  
> | Street name | street_name | Text (General) | - | - |  
> | Storey range | storey_range | Text (General) | - | - |  
> | Floor area sqm | floor_area_sqm | Numeric (General) | sqm | - |  
> | Flat model | flat_model | Text (General) | - | - |  
> | Lease commence date | lease_commence_date | Datetime (Year) "YYY" | - | - |  
> | Remaining lease | remaining_lease | Text (General) | - | - |  
> | Resale price | resale_price | Numeric (General) | $ | - | 
>  
> Compare them with the data types in DuckDB's table. 

**Examples:**

See all columns:

```sql
SELECT
  *
FROM
  resale_flat_prices_2017;
```

Select specific columns and sort by price (ascending is default):

```sql
SELECT
  town, flat_type, resale_price
FROM
  resale_flat_prices_2017
ORDER BY
  resale_price;
```

Sort by price from highest to lowest:

```sql
SELECT
  *
FROM
  resale_flat_prices_2017
ORDER BY
  resale_price DESC;
```

Sort by town alphabetically, then by price (highest first):

```sql
SELECT
  town, street_name, resale_price
FROM
  resale_flat_prices_2017
ORDER BY
  town ASC,
  resale_price DESC;
```

**Exercise**

- Select any 3 columns from the table.  
- Select flats from highest to lowest resale price in Punggol. 

> **What’s Your Query?**  
> -  **QUESTION** “If our table had 100 columns and a million rows, what would happen to our computer's memory if we always used `SELECT *`?”

***

#### Task 2: Transformations & Filtering

##### Operators

Mathematical Operators are used to perform mathematical operations on data.

| Operator | Description |
| --- | --- |
| `+` | Addition |
| `-` | Subtraction |
| `*` | Multiplication |
| `/` | Division |
| `%` | Modulo | 

Example – calculate price in thousands and rename for clarity:

```sql
SELECT
  street_name,
  resale_price / 1000 AS price_k  -- Use AS to rename the column
FROM
  resale_flat_prices_2017
WHERE
  town = 'PUNGGOL'
  AND resale_price > 500000
ORDER BY
  resale_price DESC;
```

**Exercise**

“I want to find a home for my parents. They need something larger than 100sqm, but my budget is strictly under $600,000. How would we write that rule?”

> **What’s Your Query?**

##### Filters

Filters are used to filter data based on a condition. The `WHERE` clause is used to filter data in a `SELECT` statement and commonly uses comparison and logical operators. 

| Operator | Description |
| --- | --- |
| `=` | Equal |
| `<>` | Not equal |
| `>` | Greater |
| `>=` | Greater or equal |
| `<` | Less |
| `<=` | Less or equal |
| `AND` | Logical AND |
| `OR` | Logical OR |
| `NOT` | Logical NOT | 

Example:

```sql
SELECT
  *
FROM
  resale_flat_prices_2017
WHERE
  town = 'BUKIT MERAH';
```

You can introduce line breaks to make the query more readable. 

> **Exercise – basic filters**  
> - Select flats with floor area greater than 100 sqm.  
> - Select flats with resale price between 400,000 and 500,000.  
> - Select flats with lease commence date later than year 2000 and floor area greater than 100 sqm. 

##### Advanced Filters (Optional)

Sometimes basic `=` and `>` are not enough; here are a few powerful shortcuts you can use in your `WHERE` clause. 

**`IN` – one of several values**

```sql
SELECT
  *
FROM
  resale_flat_prices_2017
WHERE
  town IN ('BUKIT MERAH', 'BUKIT TIMAH');
```

**`BETWEEN` – numbers in a range**

```sql
SELECT
  *
FROM
  resale_flat_prices_2017
WHERE
  resale_price BETWEEN 400000 AND 500000;
```

**`LIKE` – pattern matching**

```sql
SELECT
  *
FROM
  resale_flat_prices_2017
WHERE
  town LIKE 'B%';
```

**`DISTINCT` – remove duplicates**

```sql
SELECT DISTINCT
  town
FROM
  resale_flat_prices_2017;
```

> **Optional Exercise – advanced filters**  
> - Return the unique flat types and flat models.  
> - Find all towns starting with “P”. 

##### Functions

Functions are used to perform operations on data, returning new values. 

| Function | Description |
| --- | --- |
| `ABS()` | Absolute value of a number |
| `ROUND()` | Round to a number of decimal places |
| `LOWER()` | String in lowercase |
| `UPPER()` | String in uppercase |
| `LENGTH()` | Length of a string |
| `TRIM()` | Remove leading and trailing spaces |
| `CONCAT()` | Concatenate strings | 

Example:

```sql
SELECT
  LOWER(town) AS town_lower,
  CONCAT(block, ' ', street_name) AS address
FROM
  resale_flat_prices_2017;
```

***

### Q&A

Common Hurdle: “Do I need to capitalize `SELECT`?”  
Short answer: SQL keywords are not case-sensitive, but we use uppercase for keywords to make queries easier to read.

### Reflection

-  **Business Use Case:** How would a real estate app like PropertyGuru use these filters when a user moves a slider on their screen?

***

# Section 2: Finding the Big Picture (Aggregates & Grouping)

### Learning Objectives

Learners will be able to summarize data using aggregate functions and use the HAVING clause to filter those summaries.

***

### 2.1 Aggregate Functions

Aggregate functions perform calculations over many rows and return a single value. 

| Function | Description |
| --- | --- |
| `COUNT()` | Number of rows |
| `SUM()` | Sum of values |
| `AVG()` | Average value |
| `MIN()` | Minimum value |
| `MAX()` | Maximum value |
| `FIRST()` | First value in a column |
| `LAST()` | Last value in a column | 

#### Workshop – Task 3: Basic Aggregates

How many transactions happened?

```sql
SELECT
  COUNT(*)
FROM
  resale_flat_prices_2017;
```

What is the most expensive flat ever sold in this dataset?

```sql
SELECT
  MAX(resale_price)
FROM
  resale_flat_prices_2017;
```

Average resale price overall:

```sql
SELECT
  AVG(resale_price) AS avg_price_overall
FROM
  resale_flat_prices_2017;
```

Average price per town:

```sql
SELECT
  town,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  town;
```

> **Exercise – more aggregates**  
> - Select the average resale price of flats in Bishan.  
> - Select the total resale value (price) of flats in Tampines. 

***

### GROUP BY & HAVING

> **Question** “Look at this query. If you want to find out the town where the average price is more than $600,000, can you use `WHERE` as a filter? Run this SQL and find out what's wrong.”

Incorrect attempt:

```sql
SELECT
  town,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
WHERE
  avg_price > 600000
ORDER BY
  avg_price DESC;
```

**Narrative:**

“We know `WHERE` filters individual rows **before** they are grouped. Now, you only want to see towns where that average is over $600,000. You can't use `WHERE` because the average didn't exist until you grouped them. To see the towns where the average is over $600,000, you need to **GROUP** all flats by town first and then calculate their average prices. For such cases, we use `HAVING`. Think of `WHERE` as the *pre-filter* and `HAVING` as the *post-grouping filter*.”

#### Deeper Dive: WHERE vs HAVING Side-by-Side 

```sql
-- Filter on individual rows before grouping
SELECT
  town,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
WHERE
  resale_price > 500000
GROUP BY
  town;
```

```sql
-- Filter on the aggregated result after grouping
SELECT
  town,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  town
HAVING
  AVG(resale_price) > 500000;
```

> Notice how the first query throws away rows cheaper than 500,000 before averaging, while the second keeps all rows but hides towns whose **average** is below 500,000. 

***

### Group By

The `GROUP BY` clause groups rows with the same values so you can calculate summaries per group. It comes after `WHERE` and before `ORDER BY`. 

#### Task 4: Group By & Having

Filter to show only towns with high average prices:

```sql
SELECT
  town,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  town
HAVING
  avg_price > 600000
ORDER BY
  avg_price DESC;
```

##### Extra Patterns 

Group by multiple columns:

```sql
SELECT
  town,
  lease_commence_date,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  town, lease_commence_date;
```

Use column positions in `GROUP BY`:

```sql
SELECT
  town,
  lease_commence_date,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  1, 2;
```

Combine with sorting:

```sql
SELECT
  town,
  lease_commence_date,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  town, lease_commence_date
ORDER BY
  town,
  lease_commence_date DESC;
```

> **Exercise – grouped summaries**  
> - Select the average resale price by flat type.  
> - Select the average resale price by flat type and flat model.  
> - Select the average resale price by town and lease commence date only for lease commence dates after year 2010 and sort by town (descending) and lease commence date (descending). 

***

### Q&A

Common Hurdle: “What happens if I forget the `GROUP BY` but use an `AVG()`?”  
Most databases will either error or aggregate the whole table instead of per town; always list the non-aggregated columns in both `SELECT` and `GROUP BY`.

### Reflection

-  **Business Use Case:** If you are a government planner, how does `GROUP BY town` help you decide where to build the next MRT station or school?

***

# Section 3: Advanced Logic & Data Cleaning

### Learning Objectives

Learners will be able to categorize data using `CASE`, convert data types with `CAST`, and extract specific components from dates.

### Theory Recap – The "Translator"

"Data isn't always ready for analysis. A `month` might be a text string like `'2017-01'` instead of a date object. `CAST` (or the `::` shorthand) acts as our translator to fix types. Meanwhile, `CASE` allows us to create new labels—like tagging a flat as 'Large' or 'Small' based on its type—making our data much easier to read for a non-technical boss."

***

### Workshop

#### Task 5: Categorizing with CASE

**Price Categories**

```sql
SELECT
  town,
  resale_price,
  CASE
    WHEN resale_price > 1000000 THEN 'Million Dollar Club'
    WHEN resale_price > 500000 THEN 'Mid-Range'
    ELSE 'Entry-Level'
  END AS price_category
FROM
  resale_flat_prices_2017;
```

Categorize flat sizes:

```sql
SELECT
  town,
  flat_type,
  CASE
    WHEN flat_type IN ('1 ROOM', '2 ROOM', '3 ROOM') THEN 'Small'
    WHEN flat_type = '4 ROOM' THEN 'Medium'
    ELSE 'Large'
  END AS flat_size
FROM
  resale_flat_prices_2017;
```

> **Exercise – custom categories**  
> - Design your own “budget/mid/high-end” categories based on resale_price.  
> - Design a “old vs new” label based on lease_commence_date. 

***

#### Task 6: Dates and Casting

**Warm-Up: Casting Numbers**

```sql
SELECT
  town,
  resale_price,
  CAST(resale_price AS INTEGER) AS resale_price_int
FROM
  resale_flat_prices_2017;
```

or using shorthand:

```sql
SELECT
  town,
  resale_price,
  resale_price::INTEGER AS resale_price_int
FROM
  resale_flat_prices_2017;
```

**Main Task – Convert text to a real date and extract the year**

```sql
SELECT
  month,
  CONCAT(month, '-01')::DATE AS transaction_date,
  date_part('year', (month || '-01')::DATE) AS sale_year
FROM
  resale_flat_prices_2017;
```

> **Question** “If the `month` column is text `'2017-01'`, can we add 1 month to it directly? Why do we need to `CAST` it to a `DATE` type first?”

##### Optional: Advanced – Changing the Table Schema

Sometimes you may want to permanently store the converted date and year in the table. 

Convert the `month` text to a `date` and add as a new column:

```sql
SELECT
  *,
  CONCAT(month, '-01')::DATE AS transaction_date
FROM
  resale_flat_prices_2017;
```

Add a new column and fill it:

```sql
ALTER TABLE
  resale_flat_prices_2017
ADD COLUMN
  transaction_date DATE;

UPDATE
  resale_flat_prices_2017
SET
  transaction_date = CONCAT(month, '-01')::DATE;
```

Extract the transaction year:

```sql
SELECT
  *,
  date_part('year', transaction_date) AS transaction_year
FROM
  resale_flat_prices_2017;
```

***

### Q&A

Common Hurdle: “What does the `::` mean?”  
It is shorthand for `CAST(value AS datatype)`; for example, `resale_price::INTEGER` is the same as `CAST(resale_price AS INTEGER)`. 

### Reflection

-  **Business Use Case:** Why is it important to standardize date formats when merging data from two different countries?

***
