# The Golden Rules: What to Tell and What NOT to Tell

During an Application Support Analyst interview (especially at a major bank like BNP Paribas), *how* you answer is just as important as *what* you answer. Here is your cheat sheet for what to say and what to avoid.

---

## 1. Handling Outages & P1 Incidents
* **✅ WHAT TO TELL**: 
  * Tell them your primary focus during an incident is **restoring service as fast as possible** (using workarounds if needed) and keeping stakeholders informed. 
  * Mention **"MTTR" (Mean Time to Resolution)** and how you strive to minimize it.
* **❌ WHAT NOT TO TELL**: 
  * Don't say, *"I will spend hours digging into the logs to find the exact root cause while the system is down."* (Root Cause Analysis happens *after* the service is restored during Problem Management, not during a live P1).

## 2. Handling Things You Don't Know
* **✅ WHAT TO TELL**: 
  * Tell them you are incredibly resourceful. If you don't know the exact syntax, say: *"I don't have the exact Sybase outer-join syntax memorized, but I know conceptually how to build it. In a real scenario, I would pull up the documentation or check our internal Knowledge Base (KEDB) to get the syntax quickly so I can solve the issue."*
* **❌ WHAT NOT TO TELL**: 
  * Don't just say *"I don't know,"* *"I've never done that,"* or *"That wasn't my job."* Never leave a dead-end answer. Always pivot to what you *do* know.

## 3. Communication & Blame
* **✅ WHAT TO TELL**: 
  * Tell them you value **collaboration and blameless culture**. Say, *"If a developer pushes bad code that breaks production, I roll back the release or apply a fix to stabilize the system, and then work with the dev team to ensure it doesn't happen again."*
* **❌ WHAT NOT TO TELL**: 
  * Never blame other teams. Don't say, *"The developers always write bad code,"* or *"The network team is always slow."* Banks value diplomats who can work across silos.

## 4. Automation & Ansible
* **✅ WHAT TO TELL**: 
  * Tell them you use automation (Ansible) to **reduce human error, ensure consistency, and save the team time**. Emphasize that your playbooks are *idempotent* (safe to run multiple times).
* **❌ WHAT NOT TO TELL**: 
  * Don't say you automate things "because you are lazy" or "because you don't like doing manual work." While it's a common joke in IT, in a banking interview, frame automation purely around **risk reduction** and **efficiency**.

## 5. Escalation
* **✅ WHAT TO TELL**: 
  * Tell them you know when to ask for help. *"If I am working on a critical issue and I haven't made progress in 15 minutes, I will escalate to the L3 team, the DBA, or the vendor to ensure we don't breach SLA."*
* **❌ WHAT NOT TO TELL**: 
  * Don't say, *"I will work on it alone until I figure it out, no matter how long it takes."* In support, hero complexes cause SLA breaches. Knowing when to escalate is a sign of seniority.
