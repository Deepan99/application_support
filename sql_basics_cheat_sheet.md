# SQL Basics & Concepts for Application Support

In Application Support, you rarely design databases. Instead, your primary job is **querying data** to investigate client issues, and occasionally **updating data** to fix a stuck transaction.

Here are the absolute essentials you must know.

---

## 1. The Core Commands (CRUD)

### A. Read Data (`SELECT`)
This is what you will use 90% of the time to investigate issues.
```sql
-- Get everything from a table
SELECT * FROM transactions;

-- Get specific columns with a condition
SELECT trade_id, status, amount 
FROM transactions 
WHERE status = 'FAILED' AND trade_date >= '2026-07-01';

-- Pattern matching (Find a name starting with 'Bank')
SELECT * FROM clients WHERE client_name LIKE 'Bank%';

-- Checking multiple specific values
SELECT * FROM transactions WHERE status IN ('PENDING', 'FAILED', 'HELD');
```

### B. Update Data (`UPDATE`)
Used to manually fix bad data or push a stuck transaction forward.
> **⚠️ CRITICAL RULE:** ALWAYS use a `WHERE` clause. If you run `UPDATE transactions SET status='COMPLETE'`, you will update *every single row* in the table and cause a catastrophic P1 incident.

```sql
UPDATE transactions 
SET status = 'RETRY' 
WHERE trade_id = 987654;
```

### C. Insert & Delete (`INSERT`, `DELETE`)
```sql
-- Adding a missing record
INSERT INTO clients (client_id, client_name, region) 
VALUES (101, 'Acme Corp', 'EMEA');

-- Removing a bad record (Always use WHERE!)
DELETE FROM transactions WHERE trade_id = 987654;
```

---

## 2. JOINS (Combining Tables)
Databases store data across many tables. To get a full picture, you must "join" them.

* **INNER JOIN**: Returns only the rows that have matching values in both tables.
* **LEFT JOIN**: Returns all rows from the left table, and the matched rows from the right table (useful if some transactions don't have a matching client record yet).

```sql
-- Find all failed transactions and show the client's name next to them
SELECT t.trade_id, t.status, c.client_name 
FROM transactions t
INNER JOIN clients c ON t.client_id = c.client_id
WHERE t.status = 'FAILED';
```

---

## 3. Aggregation (Grouping Data)
Used when a manager asks: *"How many failed trades did we have today grouped by client?"*

```sql
SELECT client_id, COUNT(*) as total_failures
FROM transactions
WHERE status = 'FAILED' AND trade_date = '2026-07-15'
GROUP BY client_id
ORDER BY total_failures DESC;
```

---

## 4. Crucial Database Concepts

### 1. Transactions (`COMMIT` & `ROLLBACK`)
In enterprise databases like Oracle and Sybase, when you run an `UPDATE` or `DELETE`, the change is **not saved permanently immediately**.
* **`COMMIT;`**: Saves your changes permanently.
* **`ROLLBACK;`**: Undoes your changes if you made a mistake.
* *Interview Tip*: If asked how you update data safely, say: *"I run the UPDATE, verify the row count affected, and only then do I type COMMIT. If the row count is wrong, I type ROLLBACK."*

### 2. Indexes
An index is exactly like the index at the back of a textbook. 
* Without an index, the database must scan every single row in the table (a **Full Table Scan**) to find `trade_id = 123`, which takes forever and spikes CPU.
* With an index on the `trade_id` column, the database jumps instantly to the correct row.

### 3. Primary Key & Foreign Key
* **Primary Key**: A column that uniquely identifies a row (e.g., `trade_id`). It cannot be null and must be unique.
* **Foreign Key**: A column in one table that links to the Primary Key of another table (e.g., `client_id` in the transactions table). This enforces "Referential Integrity" so you can't insert a transaction for a client that doesn't exist.
