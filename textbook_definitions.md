# Textbook Definitions & Terminology Cheat Sheet

Sometimes interviewers are just checking boxes on an evaluation sheet and want to hear exact, formal definitions. Use this cheat sheet to give them the textbook terminology, which you can then back up with your practical experience.

---

## 1. Security & Compliance Definitions

* **Vulnerability**: "A weakness in an information system, system security procedures, internal controls, or implementation that could be exploited or triggered by a threat source."
* **Threat**: "Any circumstance or event with the potential to adversely impact organizational operations through unauthorized access, destruction, disclosure, or modification of information."
* **Risk**: "The potential for loss, damage, or destruction of an asset as a result of a threat exploiting a vulnerability." *(Formula: Risk = Threat x Vulnerability)*
* **Patch Management**: "The systematic notification, identification, deployment, installation, and verification of operating system and software updates."

## 2. ITIL / ITSM (Service Management) Definitions

* **Incident**: "An unplanned interruption to an IT service or reduction in the quality of an IT service." *(Goal: Restore normal service operation as quickly as possible.)*
* **Problem**: "The underlying cause of one or more incidents." *(Goal: Identify root causes and manage workarounds or known errors.)*
* **Workaround**: "A solution that reduces or eliminates the impact of an incident or problem for which a full resolution is not yet available."
* **Known Error**: "A problem that has a documented root cause and a workaround." *(Stored in the Known Error Database - KEDB).*
* **SLA (Service Level Agreement)**: "A documented agreement between a service provider and a customer that identifies both services required and the expected level of service."
* **Change Management**: "The process responsible for controlling the lifecycle of all changes, enabling beneficial changes to be made with minimum disruption to IT services."
  * *Standard Change*: Pre-authorized, low risk, relatively common.
  * *Normal Change*: Must go through the Change Advisory Board (CAB) for assessment and authorization.
  * *Emergency Change*: Must be introduced as soon as possible to resolve a major incident (approved by ECAB).
* **PIR (Post-Implementation Review)**: "A review that takes place after a change or a project has been implemented. It determines if the change was successful and identifies opportunities for improvement." *(Also known in DevOps as a Retrospective or Blameless Post-Mortem).*

## 3. DevOps & Automation Definitions

* **CI/CD (Continuous Integration / Continuous Delivery)**: 
  * *Continuous Integration*: "The practice of automating the integration of code changes from multiple contributors into a single software project frequently."
  * *Continuous Delivery*: "The automated process of pushing code changes to non-production and production environments in a safe and sustainable way."
* **IaC (Infrastructure as Code)**: "The management and provisioning of infrastructure through machine-readable definition files (like Terraform or Ansible), rather than through physical hardware configuration or interactive configuration tools."
* **Idempotence (Ansible specific)**: "The property of certain operations in mathematics and computer science whereby they can be applied multiple times without changing the result beyond the initial application. In Ansible, it means a playbook can safely run multiple times, and it will only make changes if the system is not in the desired state."
* **High Availability (HA)**: "A characteristic of a system which aims to ensure an agreed level of operational performance, usually uptime, for a higher than normal period."
