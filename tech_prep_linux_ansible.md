# Technical Prep: Linux, HP-UX, Ansible & Monitoring

This guide covers core commands, concepts, and scenario-based interview questions to help you showcase your expertise in Linux/HP-UX environments and automation using Ansible.

---

## 1. Linux & HP-UX Troubleshooting Commands

In an Application Support role, you are expected to quickly identify system bottlenecks. Below is a cheat sheet of diagnostic commands.

### System & Resource Monitoring
* **Disk Space (Linux)**: `df -h` (human-readable disk space usage), `du -sh *` (size of files/folders in the current directory).
* **Disk Space (HP-UX)**: `bdf` (HP-UX specific command equivalent to `df -h`).
* **Memory Usage**: 
  * Linux: `free -m` or `free -g` (shows total, used, and free memory).
  * HP-UX: `vmstat` or `swapinfo` (to check swap space utilization).
* **CPU & Overall Performance**: 
  * Linux: `top` (live process monitoring), `uptime` (load average for the last 1, 5, and 15 minutes).
  * HP-UX: `glance` (interactive system performance tool, highly powerful and unique to HP-UX) or `sar` (System Activity Reporter).

### Process Management
* **Find a running process**: `ps -ef | grep <process_name>`
* **Find which process is using a specific port**: 
  * Linux: `lsof -i :<port>` or `netstat -anp | grep <port>`
  * HP-UX: `netstat -an | grep <port>`
* **Kill a hung process safely**: 
  * First try graceful termination: `kill <PID>` (SIGTERM / signal 15)
  * If unresponsive, force kill: `kill -9 <PID>` (SIGKILL / signal 9)
* **Check opened files by a process**: `lsof -p <PID>` (critical when hitting "Too many open files" errors).

### Log Inspection & Text Processing
* **Follow live log updates**: `tail -f /path/to/app.log`
* **Search for errors**: `grep -i "error\|exception\|failed" app.log`
* **Search inside compressed log files**: `zgrep -i "error" app.log.2026-07-14.gz`
* **Find files modified in the last 24 hours**: `find /app/logs -mtime -1 -type f`
* **Extract specific columns (e.g., timestamp and error message)**: `awk -F',' '{print $1, $4}' app.log`

---

## 2. Ansible for Support & Automation

As an Application Support Analyst, you use Ansible to perform automated deployments, restart services, and roll out hotfixes or configurations across multiple servers without manual SSHing.

### Key Concepts
* **Inventory File**: Contains list of target servers/hosts grouped by environment (e.g., `[prod_web]`, `[uat_db]`).
* **Playbooks**: YAML files defining a list of tasks to execute on hosts.
* **Idempotency**: Ansible only makes changes if the target state does not match the desired state. If a service is already running, running the task again does nothing.

### Useful Ansible Commands for Support
* **Check connectivity to all inventory servers**:
  ```bash
  ansible all -m ping
  ```
* **Run a quick ad-hoc shell command (e.g., check disk space on all web hosts)**:
  ```bash
  ansible web -m shell -a "df -h"
  ```
* **Run a specific playbook**:
  ```bash
  ansible-playbook -i inventories/prod site.yml
  ```
* **Dry Run (Check mode - simulates execution without making changes)**:
  ```bash
  ansible-playbook site.yml --check
  ```
* **Run tasks matching a specific tag (e.g., just restarting the application)**:
  ```bash
  ansible-playbook site.yml --tags "restart_app"
  ```
* **Limit playbook execution to a single host**:
  ```bash
  ansible-playbook site.yml --limit "prod-web-01"
  ```

---

## 3. Application Monitoring & Alerts

### Support Best Practices
1. **Differentiate Alert Severity**: 
   * **Critical (P1/P2)**: Immediate client/business impact (e.g., application process down, API returning 500s). Requires immediate paging.
   * **Warning (P3/P4)**: Impending issue (e.g., Disk space at 85%, memory usage climbing). Requires investigation during business hours.
2. **Alert Triaging Procedure**:
   * Verify if the alert is a true positive (check logs, application URL).
   * Check if there is an active Change Request (CR) or scheduled maintenance.
   * Check the **KEDB** for similar alerts to see if there is a known workaround.

---

## 4. Key Technical Q&A for the Interview

### Q1: How do you investigate a "Disk Space Full" issue on a production server?
* **Answer**:
  1. I run `df -h` (or `bdf` on HP-UX) to see which mount point is 100% full.
  2. I navigate to that directory and use `du -sh * | sort -hr` to find the largest directories and files.
  3. Usually, it's due to bloated log files or core dumps.
  4. **Important**: I never just `rm` a log file if it's currently open by a process (as Linux won't release the disk space). Instead, I truncate it using `> logfile.log` or `cat /dev/null > logfile.log`.
  5. If the files are old and not needed, I compress/archive them, or delete them if safe. I then make sure the log rotation policy is working correctly.

### Q2: An application service is down, but when you check `ps -ef`, the process is running. How do you troubleshoot?
* **Answer**:
  1. This usually indicates the process is hung, in a deadlock, or not listening on its port.
  2. I run `netstat -anp | grep <port>` or `lsof -i :<port>` to check if the process is actively listening on its designated port.
  3. I inspect the application logs using `tail -n 100` and search for heap memory errors, database connection timeout errors, or thread blocks.
  4. I can check CPU usage of the process using `top -p <PID>` (or `glance` on HP-UX) to see if it's consuming 100% CPU (busy loop) or 0% CPU (deadlocked/sleeping).
  5. If it's a Java application, I take a thread dump (`jstack <PID>`) to analyze what thread is blocked before restarting the process.

### Q3: What is the benefit of Ansible in support, and how have you used it?
* **Answer**:
  * "In my previous experience, instead of manually logging into 10 different servers to perform routine checks, deploy patches, or restart services, I used Ansible. It ensures consistency, eliminates human errors, and provides an audit trail."
  * "For example, I maintained Ansible playbooks to perform daily morning checks (verifying disk space, app process statuses, and log errors) across all servers, which automatically mailed us the health report. I also used Ansible ad-hoc commands to run quick troubleshooting checks or restart services in parallel during high-pressure incidents."
