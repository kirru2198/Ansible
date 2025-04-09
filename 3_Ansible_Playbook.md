# Ansible Playbook Training

## Ansible Modules
Ansible modules are prewritten **Python** programs that you can find in Ansible's documentation. These modules are designed to perform specific tasks like:

- **Networking**
- **Cloud management**
- **System administration**, etc.

For example, if you're working with **AWS**, there are specific modules for managing EC2 instances, ELB load balancers, and more. Similarly, there are network modules available for managing networking tasks.  
Modules handle the actual work when you run your playbook. Playbooks are essentially collections of tasks written in a specific order. Each task uses a module to perform a particular action on your target system.

Ansible has modules for various platforms like **AWS**, **Azure**, **Docker**, and more. You can also write your own modules if you have custom needs and know Python.

### What is a Playbook?
A **playbook** is a collection of tasks. Each task uses modules to perform actions on a target machine. For example, if you want to uninstall software like WebEx, your playbook might:
1. Stop any running processes.
2. Delete the software and its associated files.

The steps in a playbook should be logical, so things don’t fail (e.g., stop processes before uninstalling software). Once everything is written, Ansible will execute the tasks in the order you've defined.

### Recap:
- **Modules** do the work.
- **Playbooks** organize tasks and instructions in a structured way.

Let me know if anything is unclear, and we can go over it again!

---

## Best Practices for Writing Ansible Playbooks

### 1. Use a Single Playbook or Multiple Playbooks
- **Single Playbook**: Works well for small environments or simple tasks.
- **Multiple Playbooks**: For larger environments or complex tasks. This makes it easier to manage and scale, similar to how **Terraform** separates resources into different files.

### 2. Project Structure
- Create a **proper project structure**, typically including:
  - **Project folder**
  - **Inventory files** (list of servers)
  - **Playbooks** (the tasks to be executed)
  - **Roles** (reusable components for tasks)
  - **Variables** (to customize values in tasks)

- Keep your **inventory file** specific to the project rather than modifying the default one found in `/etc/ansible/hosts`. You can specify which inventory file to use when running a playbook.

### 3. Ansible Roles
**Roles** help organize tasks into reusable components. Each role has its own directory structure containing:
  - **tasks**: Steps to perform (e.g., install software)
  - **handlers**: Special tasks that only run when notified (e.g., restart a service)
  - **variables**: Custom values for the playbook (e.g., passwords, IP addresses)
  - **files**: Static files that need to be copied (e.g., configuration files)
  - **templates**: Files with variables to be substituted (e.g., Jinja2 templates)
  - **defaults**: Default values for variables

### 4. Why Use Roles?
- **Modularity**: Helps create a modular structure that’s easier to maintain and reuse.
- **Troubleshooting**: Easier to locate and fix issues with a well-organized project.
- **Standardization**: Makes it easier for others (or your future self) to understand and troubleshoot.
- **Community Roles**: You can use pre-written roles from **Ansible Galaxy** for common services like **MySQL**, **Apache**, etc.

### 5. Ansible Handlers
**Handlers** are special tasks that only run when notified by other tasks. For example:
- After installing software, a handler might restart the service or reboot the system if needed.
- Handlers are useful for tasks like restarting services, rebooting machines, or applying configuration changes after updates.

---

## Example of Project Structure:
```plaintext
roles/
  mysql/
    tasks/
      - main.yml
    handlers/
      - main.yml
    variables/
      - main.yml
    files/
      - mysql_config.cnf
    templates/
      - mysql_config.j2
    defaults/
      - main.yml
```

### Conclusion
By organizing your Ansible playbooks into **roles** and following a structured approach, you can make infrastructure management more efficient, modular, and easier to maintain. This also helps others understand your setup if they need to take over or troubleshoot.

---

## Practical Example of Directory Structure

### Step 1: Create Directory Structure
We created a directory structure using a single command:
```bash
mkdir -p MySQL_deploy/roles/mysql/{tasks,handlers,vars,defaults}
```

This command creates the **MySQL_deploy** parent directory and the **roles** directory. Under **roles**, we have **MySQL** which contains subfolders for **tasks**, **handlers**, **vars**, and **defaults**.

### Step 2: Visualize Directory Structure
Use the **tree** command to visualize the directory structure:
```bash
tree MySQL_deploy
```

### Step 3: Create Files
Next, we create **main.yml** files in each directory, such as:
- **tasks/main.yml**: Defines tasks like installing MySQL and ensuring the service is running.
- **handlers/main.yml**: Defines handlers to restart MySQL when necessary.
- **defaults/main.yml**: Sets default variables like the MySQL root password.

### Step 4: Write Playbook
We created a playbook called **deploy_mysql.yml** to execute the MySQL role. This playbook specifies the target machine and role to be used (MySQL).  
The playbook runs in the following order:
- Executes tasks defined in **tasks/main.yml**.
- If tasks need handlers (like restarting the service), it looks in **handlers/main.yml**.
- It checks for variables in **vars/main.yml** and defaults in **defaults/main.yml**.

### Step 5: Run the Playbook
Finally, we ran the playbook:
```bash
ansible-playbook deploy_mysql.yml
```
This installs **MySQL** on the target machine and configures it based on the defined roles and variables.

---

## EFK Stack (Elasticsearch, Fluentd, Kibana)

### Introduction to Logging in Microservices
In traditional applications (monolithic), monitoring is simple: you run the application on a server and monitor logs generated by the application. However, with **microservices**, there are multiple independent applications running in different languages and environments, each generating logs in different formats.

### Problem with Logs in Microservices
- Logs are generated in various formats, making it difficult to extract meaningful information.
- Monitoring involves more than just logs; tracking **CPU usage**, **memory usage**, etc., becomes complex.

### Solution: ElasticSearch
To store logs efficiently, we use **ElasticSearch**:
- ElasticSearch is a specialized database designed for searching and logging, ideal for handling large volumes of data.
- It allows you to efficiently search and monitor logs across multiple microservices in a structured format.

### EFK Stack Components
1. **ElasticSearch**: Stores logs in a structured way, making it easy to search and analyze.
2. **Fluentd**: Collects logs from multiple services and standardizes them into a common format.
3. **Kibana**: Visualizes the logs in an easy-to-understand dashboard.

### Why Use EFK Stack?
- Microservices generate logs in different formats, which can be standardized using **Fluentd**.
- Logs are stored and indexed in **ElasticSearch** for fast search and analysis.
- **Kibana** allows you to visualize the logs in real-time, making monitoring and analysis easier.

### Deployment Overview
- **Fluentd** runs on all nodes to collect logs from the applications.
- **ElasticSearch** runs as a **StatefulSet** for reliable data storage.
- **Kibana** runs as a **Deployment**, visualizing data from **ElasticSearch**.

### DevOps Role
DevOps engineers are responsible for deploying and configuring the EFK stack:
- **ElasticSearch**: For storing logs.
- **Fluentd**: For collecting and standardizing logs.
- **Kibana**: For visualizing the logs.

### Kibana Replica Setup
In production, you should deploy multiple Kibana pods for high availability. For simplicity, we deploy just one in the demo.

---

## Career Development Tips

### 1. Focus on Tools You’re Comfortable With
Identify tools you’re comfortable with (e.g., **Docker**, **AWS**, **Kubernetes**) and dive deeper into them. Build real projects, like a **blogging platform** using AWS, Docker, and Kubernetes.

### 2. Update Your Resume and Online Presence
Your **online presence** (LinkedIn, GitHub) is just as important as your resume. Share projects and write articles to increase visibility.

### 3. Work on Real Projects
Use detailed project guides for tools like **Docker**, **Kubernetes**, and **Ansible** to build real projects. Share them on GitHub and LinkedIn.

### 4. Using GitHub and LinkedIn
Be active on GitHub and LinkedIn. Share projects, write articles, and get feedback from the community.

### 5. Ansible and Managing Variables
In Ansible, **variables** take priority over default values. For sensitive data like passwords, use an **Ansible Vault** to encrypt them.

### 6. Getting Help
If you're stuck, feel free to reach out via **LinkedIn**. I’m happy to assist.

### 7. Job Search for Freshers
Focus on your skills and projects. Jobs will follow if you keep learning and building.

### 8. Final Words
Concentrate on mastering tools and building real-world projects. Your skills will eventually lead you to job opportunities.
