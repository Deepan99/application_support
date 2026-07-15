# Interview Scenarios & Behavioral Prep (STAR Method)

Use this guide to practice answering scenario-based questions. The **STAR method** (Situation, Task, Action, Result) ensures your answers are structured, structured, and highlight your direct contributions.

---

## Technical & Incident Management Scenarios

### Scenario 1: Critical Server Resource Alert (Linux + Monitoring)
* **Question**: *"You receive an alert that a production server is running at 98% CPU and users are reporting major timeouts. How do you handle it?"*
* **STAR Response**:
  * **S (Situation)**: In my previous role, our monitoring tool alerted us that a critical backend processing server's CPU spiked to 98%, triggering warnings across our transaction system.
  * **T (Task)**: I needed to restore service immediately to meet our SLA and find the process causing the spike.
  * **A (Action)**: 
    1. I quickly logged into the server and ran `top` to identify the high CPU process (it was a Java batch task hung in a loop).
    2. I checked `tail -n 100` on the application log and saw repeated database lock warnings.
    3. I took a thread dump (`jstack`) of the JVM process to preserve evidence for root cause analysis.
    4. I communicated the status to the bridge call, and since it was a hung non-critical batch, I killed the process using `kill -9` to instantly free up CPU cycles.
    5. I then restarted the batch safely with a lower priority.
  * **R (Result)**: The CPU load dropped back to 15% within 2 minutes, client timeout errors ceased, and the overall SLA breach was avoided. The thread dump was later used by developers to fix the infinite loop bug.

---

### Scenario 2: Failed Automation Deployment (Ansible + Time Pressure)
* **Question**: *"An Ansible playbook you ran to update configurations in production failed on one of the servers, and the maintenance window is closing in 15 minutes. What is your action plan?"*
* **STAR Response**:
  * **S (Situation)**: During a weekend release, I ran an Ansible playbook to deploy a security configuration update to a cluster of 5 servers. The playbook failed on the 4th server due to an SSH timeout/connection issue, leaving the cluster out of sync.
  * **T (Task)**: I had to resolve the failure and ensure all 5 hosts were consistently configured before the post-release validation checks started.
  * **A (Action)**:
    1. Rather than running the entire playbook again, I isolated the issue by running the playbook targeting only the failed host using `--limit "prod-server-04"`.
    2. The execution failed again at the connection phase. I ran `ssh -v prod-server-04` manually and realized a network security route change during the maintenance window had blocked ports.
    3. I quickly engaged the network team on our emergency bridge, got the port unblocked, verified connectivity with `ansible prod-server-04 -m ping`, and successfully reran the limited playbook.
  * **R (Result)**: The configuration was successfully applied to all servers 5 minutes before the validation window, allowing the release to go live on time.

---

### Scenario 3: Recurring Issue & KEDB Maintenance (ITIL Problem Management)
* **Question**: *"Tell us about a time you resolved a recurring issue that the team was constantly fixing manually."*
* **STAR Response**:
  * **S (Situation)**: Every Monday morning, our reporting service failed due to a database connection timeout. The previous practice was simply for the support team on shift to restart the service manually.
  * **T (Task)**: I decided to address this proactively as part of Problem Management to eliminate the manual effort and improve application stability.
  * **A (Action)**:
    1. I gathered the logs from the last 4 failures and analyzed the timestamp of the failures.
    2. I discovered that a heavy database backup job was scheduled at the exact same time on Mondays, causing the DB to be unresponsive.
    3. I created an entry in our **KEDB** outlining the symptom, root cause, and temporary manual restart workaround so the Service Desk knew how to handle it in the short term.
    4. I then raised a Change Request (CR) to reschedule the backup job to Sunday night and optimized the application's reconnection pool setting in the config file.
  * **R (Result)**: After the change was deployed, the reporting service did not experience a single timeout for the next six months, saving the support team 2 hours of manual troubleshooting every week.

---

## Behavioral & Soft Skills Scenarios

### Scenario 4: High-Pressure Stakeholder Communication
* **Question**: *"How do you handle a situation where a P1 incident is ongoing, and you are being interrupted by stakeholders asking for updates while you are trying to resolve it?"*
* **STAR Response**:
  * **S (Situation)**: During a critical payment gateway outage, the business operations team was calling my desk every few minutes for updates, which diverted my attention from troubleshooting the network socket errors.
  * **T (Task)**: I needed to manage the stakeholders' expectations and restore their confidence while securing the quiet time necessary for my technical investigation.
  * **A (Action)**:
    1. I immediately established a dedicated incident bridge call and invited all key business stakeholders.
    2. I set clear expectations: *"I am going to mute my line for 15 minutes to perform a server restart and validation. I will unmute at exactly 21:30 to give you a status update, or sooner if service is restored."*
    3. I sent a structured email alert (using our standard template) so executives had written visibility.
  * **R (Result)**: By setting a clear update cadence, the stakeholders stopped calling my direct line. This allowed me to work uninterrupted with the infrastructure team, identify the routing issue, and resolve the outage within 18 minutes.

---

### Scenario 5: Teamwork, Collaboration & Knowledge Sharing
* **Question**: *"Describe a time when you shared knowledge or trained other team members to improve overall team efficiency."*
* **STAR Response**:
  * **S (Situation)**: Our support team had 3 new members who were struggling with the complex manual process of checking daily application health, leading to occasional missed steps.
  * **T (Task)**: My goal was to standardize the morning checks process, making it error-free and easy for the new joiners to learn.
  * **A (Action)**:
    1. I wrote a comprehensive Runbook documenting every server URL, path, and expected process state.
    2. To make it even simpler, I wrote an **Ansible playbook** that automated the status checks and printed a clean summary (OK/FAILED) of all systems.
    3. I conducted a 30-minute knowledge-sharing session where I walked the team through running the playbook and analyzing logs.
  * **R (Result)**: The morning check duration dropped from 45 minutes to 5 minutes. The new joiners were able to perform the checks independently starting the next day, and we eliminated manual check errors entirely.
