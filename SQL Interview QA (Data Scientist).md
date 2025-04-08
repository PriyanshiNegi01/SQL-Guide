# SQL and DB Interview QA (Data Scientists)

## Questions & Answers

### Q1: What are joins in SQL and discuss its types?

Answer:

In SQL, a **JOIN** clause is used to combine rows from two or more tables, based on a related column between them. Joins are essential for retrieving data stored across different tables in a relational database.

#### Main Types of Joins:

* **Inner join:** Most common type of join. Returns only the rows where there is a match in both tables.
* **Left Join:**  Returns all rows from the left table and the matched rows from the right table. Returns `NULL` for right table columns when no match exists.
* **Right Join:** Returns all rows from the right table and the matched rows from the left table. Returns `NULL` for left table columns when no match exists.
* **Full Outer Join:** Returns all rows when there is a match in one of the tables. Rows without a matching counterpart in the other table will show `NULL` values.

*Note:* MySQL does not support FULL OUTER JOIN directly. Use a `UNION` of a left and right join instead. |

![alt text](https://github.com/youssefHosni/Data-Science-Interview-Questions/blob/main/Figures/Joins%20in%20SQL.png)

**Extra Point:**  
* **Cross Join:** Returns the Cartesian product of two tables (i.e., every row of table1 joined with every row of table2).  
**Syntax Example:**

```sql
SELECT A.*, B.*
FROM table1 A
CROSS JOIN table2 B;
```

---

### Q2: Define the primary, foreign, and unique keys and the differences between them?

Answer:

- **Primary Key:**  
  - **Definition:** A primary key uniquely identifies each row in a table.  
  - **Characteristics:**  
    - Cannot contain `NULL` values.  
    - Must contain unique values only.  
    - There is only one primary key per table (which can be composite).  
  - **Usage:** Helps maintain **entity integrity**.

- **Foreign Key:**  
  - **Definition:** A foreign key is a column or a set of columns used to establish and enforce a link/relationship between the data in two tables.  
  - **Characteristics:**  
    - References a primary key (or unique key) in another table.  
    - Can contain `NULL` values if the relationship is optional.  
  - **Usage:** Maintains **referential integrity**.

- **Unique Key:**  
  - **Definition:** A unique key constrains values so that no duplicates exist in a column (or group of columns), similar to a primary key.  
  - **Differences from Primary Key:**  
    - A table can have multiple unique keys.  
    - Unique keys can allow one `NULL` (depending on the DBMS).  

**Comparison Table:**

| Key Type      | Uniqueness       | NULLs Allowed | Number Allowed      | Purpose                          |
|---------------|------------------|---------------|---------------------|----------------------------------|
| Primary Key   | Must be unique   | No            | One per table       | Unique row identification        |
| Foreign Key   | May have duplicates; must match primary/unique key | Yes (if optional) | Multiple per table (pointing to one or more tables) | Establish relationships between tables |
| Unique Key    | Must be unique   | Generally one allowed (varies by DBMS) | Multiple per table    | Enforce uniqueness in columns    |

---

### Q3: What is the difference between BETWEEN and IN operators in SQL?

Answer:

- **BETWEEN Operator:**  
  - **Usage:** Selects values within a specified range.  
  - **Notes:** The range is inclusive (both start and end values are included).  
  - **Example:**

    ```sql
    SELECT *
    FROM tablename
    WHERE price BETWEEN 10 AND 100;
    ```

- **IN Operator:**  
  - **Usage:** Selects rows where a column's value matches any value in a provided list.  
  - **Use Cases:** Best for categorical data but can also work with numeric values.  
  - **Example:**

    ```sql
    SELECT COLUMN
    FROM tablename
    WHERE country IN ('USA', 'Canada', 'UK');
    ```

---

### Q4: Assume you have the given table below which contains information on user logins. Write a query to obtain the number of reactivated users (Users who did not log in the previous month and then logged in the current month) ###
![Alt_text](https://github.com/youssefHosni/Data-Science-Interview-Questions/blob/main/Figures/Screenshot%202022-07-31%20201033.png)

Answer: 

First, we look at all the users who did not log in during the previous month. To obtain the last month's data, we subtract an 𝐈𝐍𝐓𝐄𝐑𝐕𝐀𝐋 of 1 month from the current month's login date. Then, we use 𝐖𝐇𝐄𝐑𝐄 𝐄𝐗𝐈𝐒𝐓𝐒 against the previous month's interval to check whether there was login in the previous month. Finally, we 𝗖𝗢𝗨𝗡𝗧 the number of users satisfying this condition.

```
SELECT 
    DATE_TRUNC('month', current_month.login_date) AS current_month,
    COUNT(*) AS num_reactivated_users 
FROM 
    user_logins current_month
WHERE
    NOT EXISTS (
      SELECT 
        *
      FROM 
        user_logins last_month
      WHERE
        DATE_TRUNC('month', last_month.login_date) BETWEEN DATE_TRUNC('month', current_month.login_date) AND DATE_TRUNC('month', current_month.login_date) - INTERVAL '1 month'
)
```

---

### Q5: Describe the advantages and disadvantages of relational database vs NoSQL databases

Answer:

#### **Relational Databases**

- **Advantages:**
  - **ACID Compliance:** Guarantees data integrity.
  - **Structured Schema:** Simplifies complex queries and enforces consistency.
  - **Standardized Query Language:** Easy to use SQL across different systems.
  - **Mature Technology:** Many robust, industry-standard RDBMS options.

- **Disadvantages:**
  - **Schema Rigidity:** Changing the schema can be challenging.
  - **Horizontal Scalability Limitations:** Scaling out (across multiple machines) can be more complex.
  - **Performance Bottlenecks:** May occur with very large datasets or heavy transactional load.

#### **NoSQL Databases**

- **Advantages:**
  - **Schema Flexibility:** Can handle unstructured or semi-structured data.
  - **Horizontal Scaling:** Designed to scale out by distributing data across many machines.
  - **High Availability:** Often built for distributed data centers and fault tolerance.
  - **Rapid Development:** Easier to iterate and add features without major schema changes.

- **Disadvantages:**
  - **Eventual Consistency:** Some NoSQL systems do not offer strong consistency guarantees.
  - **Complex Queries:** May not handle complex joins or transactions as effectively.
  - **Less Standardization:** Varying query languages and data models across systems.

![Alt_text](https://github.com/youssefHosni/Data-Science-Interview-Questions/blob/main/Figures/ezgif.com-gif-maker.jpg)

---

### Q6: Assume you are given the table below on user transactions. Write a query to obtain the third transaction of every user

![1661352126442](https://user-images.githubusercontent.com/72076328/186479648-5dcbf0a3-46e3-46b4-99ab-94feeace3dca.png)

Answer:
First, we obtain the transaction numbers for each user. We can do this by using the ROW_NUMBER window function, where we PARTITION by the user_id and ORDER by the transaction_date fields, calling the resulting field a transaction number. From there, we can simply take all transactions having a transaction number equal to 3.
![1661352088335](https://user-images.githubusercontent.com/72076328/186479695-5d2b7f36-5703-489d-87e3-6bad6ee1a9b7.jpg)

To pick the third transaction per user, you can use the **ROW_NUMBER()** window function:

```sql
SELECT user_id, transaction_date, amount
FROM (
    SELECT 
        user_id, 
        transaction_date, 
        amount,
        ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY transaction_date) AS txn_num
    FROM user_transactions
) sub
WHERE txn_num = 3;
```

**Explanation:**  
- **ROW_NUMBER()** numbers transactions per user.
- The inner query partitions by `user_id` and orders by `transaction_date`.
- The outer query filters transactions where the row number equals 3.

---

### Q7: What do you understand by Self Join? Explain using an example

Answer:

A **Self Join** is a regular join in which a table is joined with itself. This is useful when comparing rows within the same table.

#### Example 1: Comparing Rows Within the Same Table

Suppose you have a table, `employees`, with columns `employee_id`, `name`, and `department`. To find pairs of employees in the same department, you can write:

```sql
SELECT A.name AS Employee1, B.name AS Employee2, A.department
FROM employees A
JOIN employees B ON A.department = B.department
WHERE A.employee_id <> B.employee_id;
```

#### Example 2: Hierarchical Data

In an employee table where each employee may have a manager (stored as `manager_id` which references `employee_id`), a self join can help retrieve an employee’s manager name:

```sql
SELECT E.name AS Employee, M.name AS Manager
FROM employees E
LEFT JOIN employees M ON E.manager_id = M.employee_id;
```

P.S. we would need to take care of duplicates that may occur and consider them in the conditions.

---

### Q8: Write an SQL query to join 3 tables

Answer:

Assume you have three tables: `orders`, `customers`, and `products`.  
A typical join might look like:

```sql
SELECT 
    o.order_id,
    c.customer_name,
    p.product_name
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN products p ON o.product_id = p.product_id;
```

**Explanation:**  
- This query joins `orders` to `customers` using `customer_id` and then joins the result to `products` using `product_id`.

---

![1668274347333](https://user-images.githubusercontent.com/72076328/201538710-264494b8-62e7-4e36-8487-be449c1b441a.jpg)

---

### Q9: Write a SQL query to get the third-highest salary of an employee from `employee_table` and arrange them in descending order.

**Answer:**

To get the third-highest salary from the employee table, we can use the `DENSE_RANK()` or a subquery with `DISTINCT` and `LIMIT/OFFSET`. Here's a method using **subquery with `DISTINCT` and `LIMIT`** (PostgreSQL style):

```sql
SELECT DISTINCT salary
FROM employee_table
ORDER BY salary DESC
OFFSET 2 ROWS
LIMIT 1;
```
(MySQL style):
```sql
SELECT DISTINCT salary 
FROM employee_table 
ORDER BY salary DESC 
LIMIT 1 OFFSET 2;
```

Or using **DENSE_RANK()** (works in SQL Server, PostgreSQL, etc.):

```sql
SELECT *
FROM (
    SELECT *,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rank
    FROM employee_table
) ranked_salaries
WHERE rank = 3;
```

---

### Q10: What is the difference between Temporary Tables and Common Table Expressions (CTEs)?

| Feature                   | Temporary Tables                            | Common Table Expressions (CTEs)              |
|---------------------------|---------------------------------------------|-----------------------------------------------|
| **Definition**     | Physically created tables that exist for the duration of a session or until explicitly dropped.  | Virtual tables defined within the scope of a single SQL statement. |
| **Persistence**          | Persist during the session                  | Exists only during execution of a single query|
| **Reusability**          | Can be used across multiple queries          | Not reusable outside the query                |
| **Storage**              | Stored in the tempdb (disk/memory)          | Stored in memory for the duration of the query|
| **Syntax**               | `CREATE TEMPORARY TABLE`                    | `WITH cte_name AS (...)`                      |
| **Use Case**             | Large intermediate data, multiple uses      | Simpler queries, recursion, readable code     |
| **Performance Impact**   | Slightly higher overhead                    | Lightweight, good for quick transformations   |

**CTE Example:**

```sql
WITH Sales_CTE AS (
  SELECT salesperson_id, SUM(sales) AS total_sales
  FROM sales_data
  GROUP BY salesperson_id
)
SELECT *
FROM Sales_CTE
WHERE total_sales > 100000;
```

**Temporary Table Example:**

```sql
CREATE TEMPORARY TABLE top_sellers AS
SELECT salesperson_id, SUM(sales) AS total_sales
FROM sales_data
GROUP BY salesperson_id;

SELECT *
FROM top_sellers
WHERE total_sales > 100000;
```

---

### Q11: Why use Right Join When Left Join can suffice the requirement?

Answer:
In MySQL, the 𝗥𝗜𝗚𝗛𝗧 𝗝𝗢𝗜𝗡 𝗮𝗻𝗱 𝗟𝗘𝗙𝗧 𝗝𝗢𝗜𝗡 are used to retrieve data from multiple tables by joining them based on a specified condition.

Generally, the 𝗟𝗘𝗙𝗧 𝗝𝗢𝗜𝗡 is used more frequently than the 𝗥𝗜𝗚𝗛𝗧 𝗝𝗢𝗜𝗡 because it returns all the rows from the left table and matching rows from the right table, or NULL values if there is no match.

While **LEFT JOIN** is commonly used and can cover many requirements, there are specific scenarios where a **RIGHT JOIN** may be more appropriate:

𝟭. 𝗪𝗵𝗲𝗻 𝘁𝗵𝗲 𝗽𝗿𝗶𝗺𝗮𝗿𝘆 𝘁𝗮𝗯𝗹𝗲 𝗶𝘀 𝘁𝗵𝗲 𝗿𝗶𝗴𝗵𝘁 𝘁𝗮𝗯𝗹𝗲: If the right table contains the primary data that needs to be retrieved, and the left table contains supplementary data, a 𝗥𝗜𝗚𝗛𝗧 𝗝𝗢𝗜𝗡 can be used to retrieve all the data from the right table and matching data from the left table.

𝟮. 𝗪𝗵𝗲𝗻 𝘁𝗵𝗲 𝗾𝘂𝗲𝗿𝘆 𝗻𝗲𝗲𝗱𝘀 𝘁𝗼 𝗯𝗲 𝗼𝗽𝘁𝗶𝗺𝗶𝘇𝗲𝗱: In some cases, a 𝗥𝗜𝗚𝗛𝗧 𝗝𝗢𝗜𝗡 may be more efficient than a 𝗟𝗘𝗙𝗧 𝗝𝗢𝗜𝗡 because the database optimizer can choose the most efficient join order based on the query structure and the available indexes.

𝟯. 𝗪𝗵𝗲𝗻 𝘂𝘀𝗶𝗻𝗴 𝗼𝘂𝘁𝗲𝗿 𝗷𝗼𝗶𝗻𝘀: If the query requires an outer join, a 𝗥𝗜𝗚𝗛𝗧 𝗝𝗢𝗜𝗡 may be used to return all the rows from the right table, including those with no matching rows in the left table.

*However, note that many developers prefer LEFT JOIN by simply switching the table order, as it often improves readability.*

---

### Q12: Why Rank skips sequence?

Answers:

When using ranking functions, such as `RANK()` or `DENSE_RANK()`, you may see non-sequential rank values for the following reasons:

1. **Ties in Data:**  
   - With `RANK()`, if multiple rows have the same value, they receive the same rank. The next distinct value receives a rank with a gap.  
   - **Example:** If two rows rank as 1, the next row will be ranked 3.
   
2. **Using `DENSE_RANK()`:**  
   - `DENSE_RANK()` does not create gaps after ties; however, using both functions in similar queries can create differing sequences depending on the requirement.

3. **Filtering or Grouping Effects:**  
   - Changes in the dataset due to filtering or grouping may cause unexpected discontinuities in the rank sequence as the ranking resets with each partition.

**Tip:**  
Always consider which ranking function best suits your needs when ties are expected in your dataset.

### Q13: What are Indexes and How Do They Improve Query Performance?

Answer:

- **Indexes** are special lookup tables that the database search engine can use to speed up data retrieval.  
- **Advantages:**  
  - **Improved Read Performance:** Significantly reduces the amount of data scanned during searches.
  - **Faster Sorting and Filtering:** Speeds up order by and WHERE clauses.  
- **Disadvantages:**  
  - **Insert/Update Overhead:** Additional storage space and slower write operations due to index maintenance.
  - **Complexity:** Too many indexes can degrade performance.

**Syntax Example (Creating an Index):**

```sql
CREATE INDEX idx_employee_salary
ON employee_table (salary);
```

---

