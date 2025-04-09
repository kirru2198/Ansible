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

### YAML File Structure

YAML files are used in Ansible playbooks to define tasks. Each YAML file starts with `---` and includes key components:

- **Name**: Describes what the playbook will do.
- **Hosts**: Specifies the target servers.
- **Become**: Allows tasks to run as the root user.

### Tasks
Tasks define the actions to perform (e.g., install a package, start a service, configure a file).

Example:
```yaml
- name: Install Apache Web Server
  hosts: webservers
  become: true
  tasks:
    - name: Update the app cache
      apt:
        update_cache: yes
    - name: Install Apache
      apt:
        name: apache2
        state: present
    - name: Start Apache service
      service:
        name: apache2
        state: started
        enabled: yes
    - name: Configure Apache index page
      copy:
        content: "Welcome to Apache Web Server!"
        dest: "/var/www/html/index.html"
```

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

## Conclusion

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

Feel free to reach out if you have any questions. Tomorrow, we’ll cover some leftover topics and more hands-on workshops. You’ll get to implement more use cases.

See you tomorrow, and have a great day!
```

This markdown should be easy to follow and understand. Let me know if you'd like further adjustments!
