# Ansible Practice Playbooks for Application Support

As an Application Support Analyst, your Ansible playbooks will primarily focus on **monitoring, maintenance, automated recovery, and compliance**. 

Here are 5 essential playbooks to study and practice. I have included comments (`#`) in the YAML to explain what each part does.

---

## 1. Automated Health Check (Disk & Memory)
**Scenario**: Before a critical deployment or during a major incident bridge call, you need an instantaneous, synchronous health check across all nodes. 
*(Note: As you rightly pointed out, continuous passive monitoring belongs in tools like Prometheus/Alertmanager. Ansible is used here for active, on-demand "pre-flight" checks to ensure the environment is safe before executing a change.)*

```yaml
---
- name: Daily Server Health Check
  hosts: prod_servers
  become: yes # Run with sudo/root privileges
  gather_facts: yes # Gathers system information like OS, IP, Mounts

  tasks:
    - name: Check root disk space usage
      # We use the built-in ansible_mounts fact
      assert:
        that:
          # Check if available space is greater than 10% (0.1) of total space
          - mount.size_available > (mount.size_total * 0.1)
        fail_msg: "Critical: Disk space is below 10% on {{ mount.mount }}"
        success_msg: "Disk space is OK on {{ mount.mount }}"
      loop: "{{ ansible_mounts }}"
      loop_control:
        loop_var: mount
      when: mount.mount == '/' # Only check the root partition for this task

    - name: Check available memory
      assert:
        that:
          # Ensure at least 500MB of free memory
          - ansible_memfree_mb > 500
        fail_msg: "Critical: Server is running low on memory ({{ ansible_memfree_mb }} MB free)"
```

---

## 2. Nginx Installation, Configuration, and Auto-Fix
**Scenario**: Ensure Nginx is installed, configured with the correct standard template, and running. If the service is broken or the config drifts, this playbook automatically fixes it and verifies it is listening on port 80.

```yaml
---
- name: Nginx Deployment and Auto-Fix
  hosts: web_servers
  become: yes

  tasks:
    - name: Ensure Nginx is installed (Debian/Ubuntu)
      apt:
        name: nginx
        state: present

    - name: Deploy standard Nginx configuration
      template:
        src: nginx.conf.j2 # A Jinja2 template file on your Ansible controller
        dest: /etc/nginx/nginx.conf
      notify: Restart Nginx # This triggers the handler below ONLY if the file actually changed

    - name: Ensure Nginx service is started and enabled on boot
      systemd:
        name: nginx
        state: started
        enabled: yes

    - name: Verify Nginx is listening on port 80
      wait_for:
        port: 80
        delay: 2
        timeout: 15
        state: started
        msg: "Nginx failed to bind to port 80!"

  handlers:
    # Handlers only run at the very end of the playbook, and only if notified by a changed task
    - name: Restart Nginx
      systemd:
        name: nginx
        state: restarted
```

---

## 3. Log File Archiving & Cleanup
**Scenario**: A "Disk Space Full" alert triggers because application logs have grown too large. You need a playbook to compress logs older than 7 days and delete logs older than 30 days.

```yaml
---
- name: Application Log Maintenance
  hosts: prod_servers
  become: yes
  vars:
    log_dir: "/var/log/myapp"

  tasks:
    - name: Find log files older than 30 days
      find:
        paths: "{{ log_dir }}"
        patterns: "*.log"
        age: "30d"
      register: old_logs_to_delete

    - name: Delete logs older than 30 days
      file:
        path: "{{ item.path }}"
        state: absent
      loop: "{{ old_logs_to_delete.files }}"

    - name: Find log files older than 7 days to compress
      find:
        paths: "{{ log_dir }}"
        patterns: "*.log"
        age: "7d"
      register: logs_to_compress

    - name: Compress logs older than 7 days
      archive:
        path: "{{ item.path }}"
        dest: "{{ item.path }}.gz"
        remove: yes # Deletes the original uncompressed file after archiving
      loop: "{{ logs_to_compress.files }}"
```

**How the Loop Syntax Works:**
*   The `find` module in the first task saves its complex output (a JSON object) into a variable using the `register` keyword (e.g., `logs_to_compress`).
*   That output contains a list called `.files` containing all the matched files.
*   By writing `loop: "{{ logs_to_compress.files }}"`, Ansible iterates over that list.
*   Inside the loop, the special keyword `item` represents the current file object being processed. You use `{{ item.path }}` to extract just the absolute file path string to feed into the `archive` or `file` module.

---

## 4. Emergency Configuration Update
**Scenario**: You need to update a configuration file (e.g., changing a database connection string) across 50 servers simultaneously during a P1 incident.

```yaml
---
- name: Update Database Connection String
  hosts: all
  become: yes

  tasks:
    - name: Backup the existing config file before modifying
      copy:
        src: /etc/myapp/db_config.ini
        dest: /etc/myapp/db_config.ini.bak
        remote_src: yes # Source is on the remote server, not the control node

    - name: Update the DB hostname in the config file
      # lineinfile is perfect for changing a single line
      lineinfile:
        path: /etc/myapp/db_config.ini
        regexp: '^db_host=' # Look for lines starting with 'db_host='
        line: 'db_host=new-prod-db.internal.lan'
      notify: Restart Application # Triggers the handler below only if a change was made

  handlers:
    # Handlers only run at the end of the playbook, and only if notified
    - name: Restart Application
      systemd:
        name: myapp
        state: restarted
```

---

## 5. Security Compliance: User Access Management
**Scenario**: A support engineer has left, and a new one has joined. Hardcoding users in a playbook is a bad practice. This playbook uses dynamic variables that are passed at runtime or stored in Ansible Vault.

```yaml
---
- name: Support Team Access Management
  hosts: all
  become: yes
  
  # Default empty variables. In practice, you pass these at runtime:
  # ansible-playbook manage_users.yml -e "remove_user=john.doe add_user=jane.smith ssh_key_file=/secure/keys/jane.pub"
  vars:
    remove_user: ""
    add_user: ""
    ssh_key_file: ""

  tasks:
    - name: Ensure former employee is removed from all servers
      user:
        name: "{{ remove_user }}"
        state: absent
        remove: yes
      when: remove_user != "" # Safety check: Only runs if variable is provided

    - name: Ensure new employee exists
      user:
        name: "{{ add_user }}"
        state: present
        shell: /bin/bash
      when: add_user != ""

    - name: Deploy new employee's SSH public key
      authorized_key:
        user: "{{ add_user }}"
        state: present
        # The 'lookup' plugin safely reads the contents of the file on the Ansible Controller machine
        key: "{{ lookup('file', ssh_key_file) }}"
      when: add_user != "" and ssh_key_file != ""
```

---

## How to Practice and Talk About These in an Interview

1. **Syntax Check**: Mention that you always run `ansible-playbook playbook.yml --syntax-check` before running anything.
2. **Dry Run (Check Mode)**: Explain that for critical changes (like Playbook #4), you use `ansible-playbook playbook.yml --check` to see what *would* change without actually making modifications.
3. **Idempotency**: Emphasize that these playbooks are idempotent. If you run the log cleanup playbook twice in a row, the second run will simply report `OK` and do nothing, because the state has already been achieved.
