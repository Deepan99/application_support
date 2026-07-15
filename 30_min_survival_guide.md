# 30-Minute Interview Survival Guide: The "Unknowns"

You cannot say "I don't know." Instead, you use the **"Pivot Technique"**: Acknowledge the tool, state your conceptual understanding, and pivot to a tool you *do* know (like Ansible or Linux). 

Here is exactly how to handle the topics you lack experience in.

---

## 1. Databases & SQL (Oracle / Sybase / PL/SQL)
**The Reality**: You know Linux and Ansible, but you aren't a DBA.
**What to Know**: In App Support, you usually don't write databases from scratch. You run `SELECT` queries to check data, and you kill blocking sessions.

**How to Answer:**
> *"In my current role, my primary focus has been OS-level and infrastructure support, but I regularly interact with databases. I understand how to write `SELECT` queries with `JOINs` to investigate client data issues. If an application is hanging, I know how to check if there are blocking locks in Oracle/Sybase by querying the active sessions table, and if I find a block, I engage the DBA team to kill the session or analyze the PL/SQL procedure causing it."*

**Top 3 Concepts to drop in the interview:**
1. **Connection Pooling**: Mention that you frequently troubleshoot "Connection Pool Exhaustion" (when the app runs out of connections to the DB).
2. **Blocking Locks**: When two queries try to update the same row, one gets stuck. 
3. **Execution Plan**: If a query is slow, mention that you would ask the DBA to check the "Execution Plan" to see if it's missing an Index.

---

## 2. Unix Shell Scripting (`ksh`, `bash`)
**The Reality**: You know Linux commands, but haven't written complex scripts.
**What to Know**: Shell scripting is just putting the commands you already know (`grep`, `awk`, `tail`) into a file with a `for` loop or `if` statement.

**How to Answer:**
> *"While I haven't had to write complex Korn Shell (`ksh`) scripts from scratch—mostly because I prefer using Ansible for automation—I am fully comfortable reading and troubleshooting existing legacy shell scripts. I frequently read through them to understand variables, loops, and error exit codes so I can figure out why a batch job failed."*

**Top 3 Concepts to drop in the interview:**
1. **Exit Codes**: Mention you always check `$?` (the exit status of the last command). `0` means success, anything else is an error.
2. **Cron Jobs**: Mention you know how to schedule scripts using `crontab`.
3. **Pivoting to Ansible**: Always remind them that for complex logic, you'd rather write an Ansible playbook because it is *idempotent* (safer), whereas a shell script can be dangerous if run twice.

---

## 3. Jenkins & Terraform (DevOps / IaC)
**The Reality**: You know Ansible (Configuration), but not Terraform (Provisioning) or Jenkins (CI/CD Pipelines).
**What to Know**: 
* **Terraform** builds the house (creates the servers in the cloud). 
* **Ansible** furnishes the house (installs Nginx/Tomcat on those servers).
* **Jenkins** is just a UI scheduling tool that runs your scripts/playbooks automatically when developers commit code.

**How to Answer:**
> *"My core expertise is in Configuration Management using Ansible. While our dedicated DevOps team handles the Terraform provisioning of the bare servers, I understand the Infrastructure-as-Code concept perfectly. I regularly trigger and monitor Jenkins pipelines to deploy our Ansible playbooks, and if a Jenkins build fails, I check the console output to find the exact task that caused the error."*

**Top 3 Concepts to drop in the interview:**
1. **Jenkins Console Output**: This is where you look when a pipeline fails.
2. **State Files (Terraform)**: Mention that you know Terraform uses a `.tfstate` file to remember what infrastructure it has created.
3. **Immutable Infrastructure**: The idea that you don't patch a broken server, you just destroy it and use Terraform to spin up a fresh one.

---

## 4. AI Tools & Knowledge
**The Reality**: You haven't used AI in an enterprise support role.
**What to Know**: AI is a buzzword right now. They just want to know you are forward-thinking and use it for productivity.

**How to Answer:**
> *"I use AI tools like ChatGPT or GitHub Copilot as a productivity multiplier. For example, if I encounter a massive, undocumented legacy shell script or a complex PL/SQL block, I will feed snippets of it into an AI tool to quickly summarize its logic or help me write a complex `awk` or `sed` command. It significantly reduces my Mean Time to Resolution (MTTR)."*

---

## Final 5-Minute Checklist Before the Call:
1. **Breathe**. You have 5-8 years of solid support experience.
2. Remember **MTTR** (Mean Time to Resolution). Mention it often.
3. If asked a highly specific syntax question you don't know (e.g., "What is the exact syntax for a Sybase outer join?"), say: *"I don't have the exact syntax memorized, but in a real scenario, I would quickly check the documentation or use an AI assistant to get the syntax, allowing me to focus on solving the logical problem."*
