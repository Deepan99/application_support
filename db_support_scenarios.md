# 5 Common Database Problems & Fixes (App Support Perspective)

As an Application Support Analyst, you are not expected to be a DBA. However, you are the first responder when an application fails due to a database issue. 

Here are the top 5 database-related problems you will face, how to identify them, and how to fix them.

---

## 1. Connection Pool Exhaustion (Max Connections Reached)
* **The Problem**: The application needs to talk to the database, but all available connections in the "pool" are currently in use.
* **The Symptom**: Users report the app is freezing. The application logs (`app.log`) show errors like: `java.sql.SQLTransientConnectionException: Connection is not available, request timed out after 30000ms`.
* **The Fix**:
  * **Short-term Workaround**: Restart the application service (using your Ansible playbook or systemctl) to instantly flush and reset the connection pool, restoring service immediately.
  * **Permanent Fix**: Analyze *why* connections aren't being closed. It could be a bug in the application code (a developer forgot to close the connection after a query) or a sudden spike in user traffic requiring an increase in the `max-pool-size` configuration.

## 2. Blocking Locks / Deadlocks
* **The Problem**: Query A is updating a row in a table. Query B wants to update the same row but has to wait for Query A to finish. Query A gets stuck, causing Query B (and C, and D...) to pile up and hang the application.
* **The Symptom**: Specific actions in the UI (like clicking "Save Trade") just spin forever. 
* **The Fix**:
  * **Identify**: Run a DBA SQL query (like querying `v$session` and `v$lock` in Oracle, or `sp_who2` in Sybase) to find the "Lead Blocker" (the Session ID holding the lock).
  * **Action**: Engage the DBA team, or if you have the privileges, safely execute a `KILL <Session_ID>` command to terminate the stuck query. The queued queries will then instantly process.

## 3. Slow Queries (Missing Indexes)
* **The Problem**: A developer released a new feature, but the SQL query is doing a "Full Table Scan" (reading millions of rows) instead of using an Index (like looking up a word in a book's index).
* **The Symptom**: The application doesn't crash, but specific reports or searches take 10 minutes instead of 2 seconds. The database server CPU spikes to 100%.
* **The Fix**:
  * **Identify**: Ask the DBA to pull an **AWR report** (Oracle) or look at the active queries to find the specific SQL statement taking the longest time. Look at its **Execution Plan**.
  * **Action**: The immediate fix is to have the DBA create a new Index on the column being searched. The long-term fix is raising a defect to the development team to rewrite the SQL query more efficiently.

## 4. Tablespace / Disk Full
* **The Problem**: The database has literally run out of physical disk space to store new data or write transaction logs.
* **The Symptom**: The application logs show fatal SQL exceptions like `ORA-01653: unable to extend table` (Oracle) or `Transaction log is full` (Sybase/SQL Server).
* **The Fix**:
  * **Action**: This is a critical P1. You immediately contact the DBA and Storage/Infrastructure teams.
  * **Resolution**: The DBA will either add a new datafile, extend the tablespace size, or clear out old transaction logs. As an App Support Analyst, your job is to communicate the outage to stakeholders and pause any batch jobs trying to insert data until space is added.

## 5. Network/Firewall Drops
* **The Problem**: The database is up and running perfectly, but the application server suddenly cannot reach it.
* **The Symptom**: The application logs instantly flood with `Connection Refused` or `SocketTimeoutException`.
* **The Fix**:
  * **Identify**: From the Linux application server, run a quick network check: `telnet <db_hostname> 1521` (Oracle default port). If it hangs, it's a network issue.
  * **Action**: Check if there was a recent firewall change or network routing update. In many cases, it's a transient network blip. If the telnet fails, escalate to the Network team immediately, proving with your `telnet` output that the port is unreachable from the app server.
