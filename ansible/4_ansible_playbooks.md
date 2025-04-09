# Creating Ansible Playbooks

An **Ansible Playbook** is the core part of what Ansible does in its day-to-day activities. It’s where you define the actions you want to automate on remote systems.

- **Ansible Playbooks** contain all the configurations, commands, or tasks you want to execute on your remote hosts. Everything you write inside the playbook is executed on the systems that are connected to the **Ansible Master** (or the Ansible server).

## Playbook

A **Playbook** is essentially a collection of "plays." Think of it like a book of plays where each play represents a set of tasks to be executed on a specific host or group of hosts.

### Key Components of a Playbook:
- **Play**: The first level in a playbook is called a "Play". A play defines a set of tasks that will be executed on a particular set of hosts.
- You can have **multiple plays** in a playbook, with each play targeting a different group of hosts or performing different tasks.

### What is a Play?

A **Play** is the combination of tasks you want to perform on a particular host or group of hosts. Inside a play, you define the following elements:
- **Tasks**: The individual actions or commands to be executed.
- Each task within a play could have modules, notifications, handlers, and more, depending on what needs to be done.

In each play, the tasks are executed on the target hosts according to the conditions specified in the play, which is why it is called a "play."

---

### Example of an Ansible Playbook:

Let's consider an example where we want to create a playbook with two plays:

1. Execute a command on `host1`.
2. Execute a script on `host1`.
3. Execute a script on `host2`.
4. Install `nginx` on `host2`.

```yaml
---
- hosts: host1
  sudo: yes
  name: play 1
  tasks: 
    - name: Execute command 'Date'
      command: date
    - name: Execute script on server 
      script: test_script.sh

- hosts: host2
  name: play 2
  sudo: yes
  tasks: 
    - name: Execute script on server
      script: test_script.sh
    - name: Install nginx
      apt: name=nginx state=latest
```

### Breakdown of the Playbook:
- The **playbook** begins with three hyphens (`---`) to denote the start of the YAML file.
  
- **Plays**: Each play starts with a hyphen (`-`) followed by a dictionary defining:
  - **hosts**: The target hosts for this play. It can refer to a single host or a group of hosts as defined in the inventory file (`/etc/ansible/hosts`).
  - **sudo**: This specifies whether to run the tasks as a superuser (e.g., `sudo: yes`).
  - **name**: The name of the play. This is just a descriptive string for easier identification.
  
- **Tasks**: Inside each play, we define tasks:
  - **name**: A descriptive name for each task (e.g., "Execute command 'Date'"). This is just for readability and doesn't affect the execution.
  - **command**: The command to be executed on the remote host. In this case, the command `date` is executed.
  - **script**: Executes a script on the remote host (e.g., `test_script.sh`).
  - **apt**: A module used to manage packages in **Ubuntu** (or Debian-based systems). Here, it installs `nginx` and ensures it's up-to-date (`state=latest`).

### Key Points:
1. **Hyphen (-)**: Marks the beginning of both plays and tasks. Each new play or task starts with a hyphen.
2. **Hosts in Playbook**: The `hosts` field should match the host or group of hosts as defined in your inventory file (`/etc/ansible/hosts`).
3. **Sudo**: If the task requires superuser privileges, specify `sudo: yes`.
4. **Modules**: Each task uses a specific module to perform actions. For example, `command` and `apt` are modules used for different tasks.
5. **State**: For package management tasks (like `apt`), the `state` field defines the desired state of the package (e.g., `latest` for the most recent version).

---

### Important YAML Syntax Notes:
- A **YAML** file starts with `---`.
- Each play in a playbook is a dictionary (key-value pairs).
- The order of keys within a play doesn’t matter, but their structure should be followed:
  - **name**: The name of the play.
  - **hosts**: The hosts or groups of hosts targeted by this play.
  - **tasks**: The list of tasks to be executed in the play.
  
Each task in the playbook begins with a hyphen (`-`), indicating the start of a new task. The tasks will be executed in sequence on the defined hosts.

In essence, an Ansible Playbook is a list of plays that defines tasks to automate the configuration of multiple remote systems in a structured and efficient manner.
