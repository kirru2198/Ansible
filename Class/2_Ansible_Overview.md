# Ansible Overview

Ansible is an open-source tool used for automating tasks like configuration management, application deployment, and IT orchestration. Configuration management refers to managing settings for applications and systems, while IT orchestration deals with day-to-day IT activities like starting/stopping services, creating/deleting users, and modifying files.

## Ansible Architecture

Ansible has a unique architecture compared to tools like Chef or Puppet. These tools require an agent installed on the target machines (slaves), but Ansible works without an agent. Here's how it works:

- **Master**: This is the machine where Ansible is installed and runs.
- **Slave**: These are the target machines where you want to make changes.

Ansible works by using SSH to connect to these target machines (slaves). The only requirements are:
1. SSH connectivity between the master and slaves.
2. Python installed on both the master and slave machines (since Ansible is written in Python).

Ansible is referred to as "agentless" because no agent is needed on the slave machines.

### Example Setup:

Let’s say you have three Linux machines:
1. **Ansible Master**: This machine has Ansible installed with an IP of `10.5.0.9`.
2. **Slave A**: This machine has an IP of `10.5.0.4`.
3. **Slave B**: This machine has an IP of `10.5.0.8`.

These machines are on the same network (e.g., Virtual Private Cloud or VPC) and can communicate freely with each other. The key thing is that all machines need Python installed and SSH connectivity set up for Ansible to work.

### Summary:
- **Master**: Where Ansible is installed.
- **Slave**: Target machines where changes are made.
- **Agentless**: No need to install agents on slave machines.
- **Prerequisites**: SSH and Python on both master and slave machines.

This is the basic architecture, and we’ll build on this to understand how Ansible works in practice.

---

## Step 1: Install Ansible on the Master

When installing software like Ansible, always follow the official documentation. For Ansible, you can find the official installation instructions on the [Ansible Documentation Website](https://docs.ansible.com/ansible/latest/installation_guide/index.html). This ensures you're using the correct commands for your system.

### Example for Ubuntu:
1. Run `sudo apt update` to update your package list.
2. Add the Ansible repository.
3. Install Ansible with `sudo apt install ansible`.
4. Once installed, verify it by running `ansible --version`. If it outputs information about Ansible, the installation was successful.

---

## Step 2: Set Up Passwordless SSH Between Master and Slave

To run Ansible, you need passwordless SSH between the master (control node) and the slave (target node). Here’s how to set it up:

1. **Generate SSH Keys on Master**:
   - Run `ssh-keygen -t rsa` on the master node to generate SSH keys.
   - By default, this will create keys in the `~/.ssh` directory.

2. **Copy the Public Key to the Slave**:
   - Copy the public key (located in `~/.ssh/id_rsa.pub`) to the slave's `~/.ssh/authorized_keys` file.
```bash
sudo cat ./.ssh/id_rsa.pub
```
```bash
sudo nano ./.ssh/authorized_keys
```
   - Use the `ssh-copy-id` command or manually copy the key using `scp` or any other method.

3. **Test the Passwordless SSH**:
   - Try running `ssh <slave-ip>` from the master node. If it logs in without asking for a password, passwordless SSH is set up.

This setup allows Ansible to operate without needing to enter passwords each time a command is executed.

---

When testing passwordless SSH between a master node and a slave node, you should use the **private IP addresses** of the nodes if they are within the same local network or if they are part of an internal network (e.g., cloud instances in the same region or VPC).

Here’s the reasoning:
- **Private IP**: If both the master and slave nodes are within a private network (like a VPC or local network), you'll use the private IP address of the slave node to test the passwordless SSH connection.
- **Public IP**: If the nodes are on different networks (e.g., the master node is outside the private network of the slave), and the slave is reachable over the internet, then you would use the **public IP**.

But, in most cases where both nodes are part of the same local or cloud network, you'd typically use the **private IP** for testing passwordless SSH.

---

## Working with Ansible - Using an Inventory File

We are setting up a process to use Ansible for automating tasks like copying files securely via SCP (Secure Copy Protocol) and making changes to configurations on servers. To track the servers, we use an **inventory file**. This file lists all the servers (hosts), categorized by their roles (e.g., web servers, database servers).

### 1. Inventory File
The inventory file, typically located at `/etc/ansible/hosts`, contains information about the servers Ansible will manage. It's divided into groups, like:
- **Web servers**
- **Database servers**

Each server entry has either an IP address or a Fully Qualified Domain Name (FQDN).

### 2. Creating Entries
You would add entries for the web and database servers in the inventory file:
```
[Web server]
10.5.0.4
```
```
[dbserver]
10.5.0.8
```

### 3. Running Ansible Commands
To perform tasks like creating users on these servers, you can use the `ansible` command. For example, to create a user, you would use the `user` module. Since root privileges are required to add a user, you need to use the `-b` option (for "become root").

Example command to add a user:
```bash
ansible -m user -a "name=test_user password=secret" dbserver -b
```
This command creates a user on the target servers with admin rights.

### 4. Running Commands
If you run the command without root access, you might get a "Permission Denied" error. To avoid this, use the `-b` flag to elevate to root privileges, allowing you to execute commands as an administrator.

### 5. Verifying Changes
After running the command with root privileges, verify that the user was created by checking the dbserver.
```
id user
```
<img width="362" alt="image" src="https://github.com/user-attachments/assets/fc6bd7e2-4bda-4ac7-a65b-7d97875997bd" />

---

## Example: Using Ansible to Manage Users

We have two servers:
1. **Ansible Master**: The control server.
2. **Ansible Slave (DB Server)**: The target server where tasks will be executed.

On the Ansible master server, there is a Python module (a program) called `user.py`, which can manage users on other servers. Here’s how it works:

1. The Ansible master sends a command to the DB server to create a user.
2. The user model is copied to the DB server.
3. The model reads the command (e.g., create a user with a specific password) and executes it on the DB server.
4. After the user is created, the model deletes itself from the DB server.

In this example, we use the `useradd` command, but Ansible can also handle tasks like installing applications, starting services, etc.

### For Larger Setups:
If you need to manage multiple DB servers, Ansible can handle that too. Just specify a list of DB servers in the inventory file, and Ansible will execute the task on all of them.

---

## Key Points About Ansible Playbooks

Ansible uses a **declarative approach** to automate tasks. Instead of running commands individually, you can create a **playbook** (a YAML file) to define the steps. For example, to install Apache on a server, you would define the following steps in a playbook:
- Install the package.
- Configure it.
- Start the service.
- Ensure it runs on reboot.

### Create a folder myAnsiblePlayBooks

Create a folder with the name myAnsiblePlayBooks, inside which create playbooks

```bash
sudo mkdir myAnsiblePlayBooks
```
```bash
cd myAnsiblePlayBooks
```
```bash
sudo nano install_apache.yml
```
then past the yaml file 

### YAML File Structure

YAML files are used in Ansible playbooks to define tasks. Each YAML file starts with `---` and includes key components:

- **Name**: Describes what the playbook will do.
- **Hosts**: Specifies the target servers. (in this place we can also give ip address of a single server)
- **Become**: Allows tasks to run as the root user.

### Tasks
Tasks define the actions to perform (e.g., install a package, start a service, configure a file).

Example:
```yaml
- name: Install Apache on Ubuntu Server
  hosts: webservers
  become: true
  tasks:
    - name: Update the app cache
      apt:
        update_cache: yes
    - name: Install Apache2
      apt:
        name: apache2
        state: present
    - name: Start Apache service
      service:
        name: apache2
        state: present
    - name: Ensure Apache service is started and enabled 
      service:
        name: apache2
        state: started
        enabled: yes
    - name: Configure Apache index page
      copy:
        content: "<h1>Welcome to Apache Web Server!</h1>"
        dest: "/var/www/html/index.html"
```
---
To run an Ansible playbook, you use the `ansible-playbook` command. The basic syntax is as follows:

```bash
ansible-playbook <playbook_file.yml>
```

### Example:

If your playbook file is named `install_apache.yml`, you would run the following command:

```bash
ansible-playbook install_apache.yml
```

### Additional Options

- **Specify the Inventory File**: If you're using a custom inventory file instead of the default `/etc/ansible/hosts`, you can specify it with the `-i` flag:

  ```bash
  ansible-playbook -i /path/to/inventory install_apache.yml
  ```

- **Check Mode**: If you want to see what changes would be made without actually applying them, you can use the `--check` option:

  ```bash
  ansible-playbook --check install_apache.yml
  ```

- **Verbose Output**: To get more detailed output while the playbook is running, you can use the `-v` (verbose) flag:

  ```bash
  ansible-playbook -v install_apache.yml
  ```

  You can increase verbosity with `-vv`, `-vvv`, etc., for even more detailed output.

- **Limit**: If you want to run the playbook on specific hosts or groups from your inventory, you can use the `--limit` option:

  ```bash
  ansible-playbook --limit myhost install_apache.yml
  ```

  Or you can limit it to a group of hosts:

  ```bash
  ansible-playbook --limit webservers install_apache.yml
  ```

These are some common ways to run an Ansible playbook. You can explore more options using `ansible-playbook --help`.

---

## Troubleshooting

If a service like Apache fails to start, it could be because port 80 is already in use. You can free the port by stopping other services (e.g., Nginx) running on that port. Use commands like `docker system prune` to clean up containers or `lsof -i:80` to identify which service is using port 80.

---

## Simplifying the Concept of Ansible

With Ansible, you can automate tasks like installing software, configuring settings, and managing services on multiple servers. Instead of performing these tasks manually, you can use a playbook to automate them. This is useful when you need to perform the same setup on many servers, saving time and reducing errors.

### Example Use Case: Installing MySQL
The same logic applies when installing MySQL:
1. Define a playbook to install MySQL.
2. Ensure MySQL service is started and enabled.
3. Create a sample database.
---

# Installing and Configuring MySQL on Ubuntu Server Using Ansible

## Overview

In this guide, we'll walk through the steps to install MySQL on Ubuntu servers using Ansible. We'll cover the necessary tasks for installation, configuration, and database creation, all of which can be executed automatically on multiple servers.

### Playbook Breakdown

Ansible playbooks are used to automate tasks. Below is an example of a playbook used to install and configure MySQL on Ubuntu servers.

```yaml
---
- name: Install MySQL on Ubuntu Server
  hosts: dbservers
  become: true  # Run tasks as root using sudo

  vars:
    mysql_root_password: "StrongRootPassword123"  # Define the root password variable

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes  # Update the apt package index

    - name: Install MySQL server
      apt:
        name: mysql-server  # MySQL package name
        state: present  # Ensure the package is installed

    - name: Ensure MySQL service is started and enabled
      service:
        name: mysql
        state: started  # Start the MySQL service
        enabled: yes  # Enable MySQL to start on boot

    - name: Set MySQL root password
      mysql_user:
        name: root
        host: localhost
        password: "{{ mysql_root_password }}"
        check_implicit_admin: yes  # Ensure the root password is set
        no_log: true  # Hide the password from logs

    - name: Secure MySQL installation (disable remote root login)
      mysql_user:
        name: root
        host: "{{ item }}"
        state: absent  # Remove root access from remote hosts
      with_items:
        - 127.0.0.1
        - ::1
        - localhost

    - name: Create a sample database
      mysql_db:
        name: sample_db
        state: present  # Ensure the sample database is created
```

---

## Detailed Explanation of Each Step

### 1. Playbook Definition

```yaml
name: Install MySQL on Ubuntu Server
hosts: dbservers
become: true
```

- **name**: Describes the purpose of the playbook.
- **hosts**: The servers this playbook will be executed on. In this case, `dbservers` indicates a group of database servers defined in your Ansible inventory.
- **become**: Ensures the playbook runs with root privileges using `sudo`.

### 2. Variables Section

```yaml
vars:
  mysql_root_password: "StrongRootPassword123"
```

- Defines variables used in the playbook. Here, `mysql_root_password` is the root password for MySQL.

### 3. Updating apt Cache

```yaml
- name: Update apt cache
  apt:
    update_cache: yes
```

- This task updates the package index on the server before installing any packages, ensuring you have the latest version of MySQL available.

### 4. Installing MySQL Server

```yaml
- name: Install MySQL server
  apt:
    name: mysql-server
    state: present
```

- Installs the `mysql-server` package. The `state: present` ensures MySQL is installed, but doesn't reinstall if it’s already present.

### 5. Starting and Enabling MySQL Service

```yaml
- name: Ensure MySQL service is started and enabled
  service:
    name: mysql
    state: started
    enabled: yes
```

- Ensures the MySQL service is started and set to start automatically on system reboot.

### 6. Setting MySQL Root Password

```yaml
- name: Set MySQL root password
  mysql_user:
    name: root
    host: localhost
    password: "{{ mysql_root_password }}"
    check_implicit_admin: yes
    no_log: true
```

- This task sets the root password for MySQL using the variable `mysql_root_password`. The `no_log: true` prevents the password from being displayed in logs.

### 7. Securing MySQL (Disabling Remote Root Login)

```yaml
- name: Secure MySQL installation (disable remote root login)
  mysql_user:
    name: root
    host: "{{ item }}"
    state: absent
  with_items:
    - 127.0.0.1
    - ::1
    - localhost
```

- Disables remote root login by removing the root user for specific IPs (`127.0.0.1`, `::1`, and `localhost`). This is a security measure to restrict root access to localhost only.

### 8. Creating a Sample Database

```yaml
- name: Create a sample database
  mysql_db:
    name: sample_db
    state: present
```

- Creates a sample database named `sample_db` to test the MySQL installation.

---

## Running the Playbook

To execute this playbook, run the following command on your Ansible control node:

```bash
ansible-playbook install_mysql.yml
```

This command will execute the tasks defined in the playbook on all servers specified in the `dbservers` group.

---

## Benefits of Using Ansible for MySQL Installation

1. **Consistency**: Ansible ensures that MySQL is installed and configured the same way across all target servers.
2. **Scalability**: Instead of manually installing MySQL on hundreds of servers, you can use the same playbook to install it on as many servers as needed.
3. **Automation**: By using Ansible, you automate repetitive tasks and reduce the possibility of human error.
4. **Idempotency**: Ansible's declarative nature means that you can run the playbook multiple times, and it will only make changes if necessary (e.g., if MySQL is not installed or configured correctly).

---

## Declarative vs Imperative Approach

### Declarative Approach

- In the declarative approach, you **declare** the desired state of the system, and Ansible handles how to achieve that state. For example, when you declare that a package should be installed, Ansible will ensure the package is present, regardless of whether it's already installed.
- **Example in this playbook**: Using `state: present` to install the MySQL package and ensure it's running.

### Imperative Approach

- In the imperative approach, you **explicitly instruct** Ansible on how to achieve a task. For example, you might tell Ansible to install a package and then restart a service, but this approach doesn't focus on the desired end state.
- **Example**: Using shell commands or specific commands to install and configure MySQL manually.

### Benefits of the Declarative Approach

1. **Reusability**: You can reuse the same playbook on multiple servers or environments without modification.
2. **Idempotency**: If the system is already in the desired state, Ansible will not make changes.
3. **Clarity**: You describe what you want in simple terms, and Ansible takes care of the how.

---

## Conclusion

Using Ansible to install and configure MySQL provides a streamlined, repeatable, and scalable solution for managing database servers. With the declarative approach, you ensure that your infrastructure is always in the desired state, making it easier to maintain and automate.

---

Using Ansible playbooks, you can easily automate tasks across multiple servers. The playbook allows you to write simple and repeatable configurations, ensuring consistency and speed, especially when managing a large number of servers.

---

## Next Steps

To uninstall MySQL, you can follow a manual process or document it in a playbook:
1. Stop the service.
2. Remove configuration files.
3. Uninstall the software.

With a declarative approach, Ansible allows you to describe the desired outcome (e.g., stop MySQL) rather than the steps to achieve it.

---

You can check my GitHub for detailed examples and explanations. It's a great resource for real-life scenarios to help you during interviews or with daily tasks. I recommend going through it step by step to understand the concepts clearly.
