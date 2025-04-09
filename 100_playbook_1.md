Example Use Case: Installing MySQL

Define a playbook to install MySQL.
Ensure MySQL service is started and enabled.
Create a sample database.

Here's an example of an Ansible playbook that installs MySQL, ensures the MySQL service is started and enabled, and creates a sample database:

```yaml
---
- name: Install and configure MySQL
  hosts: all
  become: yes  # Run with sudo privileges

  tasks:
    - name: Install MySQL server
      apt:
        name: mysql-server
        state: present
        update_cache: yes
      when: ansible_os_family == "Debian"  # For Ubuntu/Debian-based systems

    - name: Install MySQL server (RedHat/CentOS)
      yum:
        name: mysql-server
        state: present
      when: ansible_os_family == "RedHat"  # For RedHat/CentOS-based systems

    - name: Start MySQL service
      service:
        name: mysql
        state: started
        enabled: yes

    - name: Create a sample MySQL database
      mysql_db:
        name: sample_database
        state: present
        login_user: root  # Assuming MySQL root user exists
        login_password: "{{ mysql_root_password }}"  # Use a variable for the root password

    - name: Create a sample user and grant privileges
      mysql_user:
        name: sample_user
        password: "{{ mysql_user_password }}"
        priv: "sample_database.*:ALL"
        state: present
        login_user: root
        login_password: "{{ mysql_root_password }}"
```

### Explanation:

1. **Install MySQL Server**:  
   - The playbook first installs MySQL based on the underlying OS (`apt` for Debian/Ubuntu and `yum` for RedHat/CentOS).
   
2. **Start MySQL Service**:  
   - The MySQL service is started and enabled to start at boot.
   
3. **Create a Sample Database**:  
   - A database called `sample_database` is created using the `mysql_db` module. The `login_user` and `login_password` options authenticate using the MySQL root user.

4. **Create a Sample User and Grant Privileges**:  
   - A new MySQL user called `sample_user` is created, and full privileges are granted on the `sample_database`. The password for the user is passed as a variable (`mysql_user_password`).

### Variables (Example in a separate file, e.g., `vars.yml`):

```yaml
mysql_root_password: "root_password_here"
mysql_user_password: "user_password_here"
```

You can reference this file in your playbook by adding:

```yaml
vars_files:
  - vars.yml
```

### Running the Playbook

Once you have the playbook (e.g., `install_mysql.yml`), you can run it with the following command:

```bash
ansible-playbook -i inventory_file install_mysql.yml
```

Where `inventory_file` is the path to your Ansible inventory that defines the target hosts.

This playbook ensures MySQL is installed, the service is running and enabled, and a database with a user is created for use.
