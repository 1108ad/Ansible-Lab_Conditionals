# Ansible-Lab_Conditionals

1. The playbook file
2. A `README.md` explaining its purpose and usage
3. Optional `.gitignore`

---

### 📁 Directory structure suggestion

```
nameserver-playbook/
├── nameserver.yaml
├── README.md
└── .gitignore
```

---

### ✅ `nameserver.yaml` (Your Playbook)

```yaml
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
```

---

### 📝 `README.md`

````markdown
# Nameserver Playbook

This Ansible playbook ensures that the specified nameserver (`10.0.250.10`) is added to `/etc/resolv.conf` **only if** it is not already present.

## 🔧 Use Case

Prevents duplicate entries of nameserver in `/etc/resolv.conf` when the playbook is run multiple times.

## 📋 How It Works

- First task reads the current content of `/etc/resolv.conf`.
- Second task checks if the nameserver already exists.
- If not, it appends the nameserver to the file.

## ▶️ Usage

Run the playbook using:

```bash
ansible-playbook nameserver.yaml
````

Ensure you have appropriate `sudo` permissions.

## 🔐 Prerequisites

* Ansible installed
* Localhost inventory or specified in `/etc/ansible/hosts`

## 🧼 Recommendation

For production usage, consider using the `lineinfile` module for idempotent line management.

````

---

### 🙅 `.gitignore` (Optional)

```gitignore
*.retry
*.log
*.bak
````

---

Let me know if you'd like to push this to a GitHub repo or want a version using the `lineinfile` module instead.
