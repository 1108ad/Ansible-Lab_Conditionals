# Use Case :Ansible-Lab_Conditionals

Playbook /home/bob/playbooks/nameserver.yaml attempts to add an entry in /etc/resolv.conf file to add a new nameserver.

The first task in the playbook is using the shell module to display the existing contents of /etc/resolv.conf file and the second one is adding a new line containing the name server details into the file. However, when this playbook is run multiple times, it keeps adding new entries of same line into the resolv.conf file. To resolve this issue, update the playbook as per details mentioned below.

Add a register directive to store the output of the first task to a variable called command_output

initial playbook is:-----------------------------------------------
---
- name: 'Add name server entry if not already entered'
  hosts: localhost
  become: yes
  tasks:
    - shell: 'cat /etc/resolv.conf'

    - shell: 'echo "nameserver 10.0.250.10" >> /etc/resolv.conf'"
 
---------------------------------------------------------------------

Then add a conditional to the second task to check if the output already contains the name server (10.0.250.10). Use command_output.stdout.find(<IP>) == -1
----------------------------------------------------------------------------------------------------------------------------------------------------------
Note:
a. A better way to do this would be to use the lineinfile module. This is just for practice.


b.shell and command modules are similar in a way that they are used to execute a command on the system. However, shell executes the command inside a shell giving us access to environment variables and redirection using >>.
------------------------------------------------------------------------------------------------------------------------------------------------------------
### ✅ Solution :-
You have a playbook (nameserver.yaml) that:
First, shows what's currently inside the file /etc/resolv.conf
Then, it adds a line with a DNS server: nameserver 10.0.250.10

🛑 But there’s a problem:
Every time you run this playbook, it keeps adding the same line again and again, even if it's already there!

🛠️ What You Need to Fix:
Save the output of the first command (contents of /etc/resolv.conf) using a variable — name it command_output.

Before adding the new line, check if the line already exists using:
when: command_output.stdout.find("10.0.250.10") == -1
This tells Ansible: “Only add this line if it’s not already there.”

📌 Why this matters:
It helps avoid duplicate entries in the file.

Though this can be done better using lineinfile module, you're learning how to use register and when conditions with the shell module.


Here’s the corrected playbook with the fix using register and when to avoid duplicate entries in /etc/resolv.conf:

---
- name: 'Add name server entry if not already entered'
  hosts: localhost
  become: yes
  tasks:

    - name: Read current contents of resolv.conf
      shell: cat /etc/resolv.conf
      register: command_output

    - name: Add nameserver only if not present
      shell: echo "nameserver 10.0.250.10" >> /etc/resolv.conf
      when: command_output.stdout.find("10.0.250.10") == -1
---------------------------------------------------------------------------------------------------------------------------------
### 🔍 Key Concepts Used:

| Concept           | Explanation                                                      |
| ----------------- | ---------------------------------------------------------------- |
| `register`        | Saves output of a command to a variable (`command_output`).      |
| `stdout`          | This is the actual text output from the command.                 |
| `when`            | Runs the second task **only if** the condition is true.          |
| `find(...) == -1` | Checks if the string is **not found** (i.e., needs to be added). |
