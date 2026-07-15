# ITIL Processes, KEDB & Stakeholder Communication

In a client-focused corporate environment like BNP Paribas CIB, support operations rely on standardized, disciplined processes. This guide outlines how ITIL, KEDB management, and stakeholder communication work in practice.

---

## 1. ITIL Framework in Application Support

Understanding the distinction between support processes is a critical requirement for a senior Application Support role (5-8 years).

### Incident Management
* **Goal**: Restore normal service operation as quickly as possible and minimize the adverse impact on business operations.
* **Process**: 
  * Detect/Report -> Log -> Classify -> Prioritize -> Investigate/Diagnose -> Resolve (Workaround/Fix) -> Close.
  * **Workaround**: A temporary way to restore service (e.g., restarting a service or switching to a secondary node) without fixing the root cause. This is crucial for meeting SLAs.

### Problem Management
* **Goal**: Prevent incidents from happening and minimize the impact of incidents that cannot be prevented.
* **Process**:
  * Analyze recurring incidents to find the root cause.
  * Perform **RCA (Root Cause Analysis)** and document the findings.
  * Log a **Known Error** and create a KEDB entry.
  * Implement a permanent fix (often raising a Change Request).

### Change Management
* **Goal**: Ensure standardized methods and procedures are used for efficient and prompt handling of all changes, minimizing impact on service quality.
* **Types of Changes**:
  * **Standard Change**: Pre-approved, low-risk, routine (e.g., standard patch deployment, weekly log purging).
  * **Normal Change**: Requires scheduling and approval by the **CAB (Change Advisory Board)** (e.g., major application version upgrade).
  * **Emergency Change**: Unscheduled, high-risk, resolved to fix a critical production bug. Requires approval from the Emergency CAB (ECAB).

---

## 2. Known Error Database (KEDB) & KB Maintenance

A **KEDB** is a sub-repository of the Problem Management database containing historical records of bugs, incidents, their root causes, and temporary workarounds.

### Key Benefits
* **Reduces MTTR**: L1/L2 analysts can search the KEDB using error logs or symptoms and find a tested workaround in seconds.
* **Avoids Re-inventing the Wheel**: Keeps knowledge centralized so that when team members leave, the technical solutions stay.

### Structure of a High-Quality KEDB Entry

| Field | Example Content |
| :--- | :--- |
| **KEDB ID** | KE-APS-1042 |
| **Associated Problem ID** | PRB003291 |
| **Symptom / Error Message** | `java.net.ConnectException: Connection refused: connect` in `adapter-service.log` |
| **Root Cause** | The batch file transfer system times out when sending files larger than 2GB to downstream client. |
| **Workaround** | 1. Manually split the file into 500MB chunks using Linux `split` command.<br>2. Run the script `/app/bin/resend_chunks.sh`. |
| **Permanent Solution** | Upgrade the SFTP client library to version 3.4.1 (Scheduled for Release v2.8). |

---

## 3. Stakeholder Communication Templates

Clear communication during high-pressure production incidents is vital. BNP Paribas client-focused support requires sending regular updates using professional templates.

### Template 1: Major Incident (P1/P2) Notification (Initial Alert)
> **Subject**: [MAJOR INCIDENT] [INITIAL] degraded performance on Cash Management Web App (CM-PROD)
> 
> **Dear Stakeholders,**
> 
> Please be informed that we are experiencing a high-priority incident affecting the Cash Management Web Portal.
> 
> * **Business Impact**: Clients are experiencing timeouts when attempting to view real-time balances.
> * **Target Systems**: CM-PROD (Web servers 01 and 02)
> * **Severity**: P2 (High)
> * **Incident Start Time**: 2026-07-14 20:15 UTC
> * **Current Status**: The Application Support team is actively investigating the issue. Initial logs indicate database connection pool exhaustion. We are currently engaging the DBA team to analyze active locks.
> 
> Next update will be provided in **30 minutes** (by 20:50 UTC) or upon resolution.
> 
> **Best regards,**  
> **2S APS Support Team**

### Template 2: Major Incident (P1/P2) Resolution Notification
> **Subject**: [RESOLVED] degraded performance on Cash Management Web App (CM-PROD)
> 
> **Dear Stakeholders,**
> 
> We are pleased to inform you that the issue affecting the Cash Management Web Portal has been resolved.
> 
> * **Incident Resolution Time**: 2026-07-14 20:42 UTC
> * **Total Outage Duration**: 27 minutes
> * **Action Taken**: The DBA team identified and killed a blocked session holding lock on the balance table. The application connection pool has recovered, and system metrics are back to normal.
> * **Validation**: Real-time balance queries were verified successfully by our business operations test team.
> 
> A formal Root Cause Analysis (RCA) will be conducted, and a Problem ticket will be opened.
> 
> **Best regards,**  
> **2S APS Support Team**

---

## 4. Key ITIL & Process Q&A for the Interview

### Q1: What is the difference between an Incident and a Problem? Can you give an example?
* **Answer**:
  * "An **Incident** is an unplanned interruption to a service or a reduction in its quality. The primary goal of Incident Management is to restore the service to normal as fast as possible using a workaround if needed."
  * "A **Problem** is the underlying cause of one or more incidents. The goal of Problem Management is to find the root cause, document workarounds in the KEDB, and find a permanent fix."
  * **Example**: "If a server's disk space hits 100% and crashes the application, restarting the server and clearing logs is **Incident Management** (restores service). Investigating *why* the logs grew so rapidly, finding a bug in the log rotation configuration, and fixing it is **Problem Management**."

### Q2: How do you maintain and update a KEDB in your day-to-day operations?
* **Answer**:
  * "Whenever we solve an incident that does not have an immediate permanent fix, or when we identify a recurring pattern, we log a Problem Ticket."
  * "As a senior support analyst, I document the symptoms, specific error logs, and the verified workaround in our KEDB (in tools like ServiceNow or Confluence)."
  * "I also review KEDB articles regularly to archive outdated workarounds after a software release deploys the permanent fix. This keeps the database clean and useful for the L1/Service Desk teams."
