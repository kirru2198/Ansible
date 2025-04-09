# How Does Ansible Work?

At the core of Ansible, the configuration management tool, lies a component known as **Ansible Playbooks**. Ansible Playbooks are where you define what exactly you want to configure on remote systems. Essentially, these playbooks are a collection of instructions that Ansible will execute on the target systems.

## Ansible Playbooks

An **Ansible Playbook** is where you write the code to specify the configuration tasks you want to perform on the remote systems. Everything that is written in the Ansible Playbook will be executed on the remote machines once the playbook is run.

### Role of Ansible Playbooks

Ansible Playbooks play a **key role** in the functioning of the Ansible ecosystem. Without playbooks, Ansible would not know what tasks to perform on the target systems. These playbooks are the heart of Ansible’s automation process.

## Syntax of Ansible Playbooks

Ansible Playbooks are written in **YAML** (Yet Another Markup Language). YAML is a human-readable data serialization format that's easy to write and understand, making it ideal for configuration files like Ansible Playbooks.

### Example Scenario: Josh Wants to Install Apache Tomcat

Let's consider an example where Josh wants to configure (install) Apache Tomcat on multiple remote systems. Here's how he would do it:

1. **Write the Playbook**: Josh writes an Ansible Playbook where he specifies the software (in this case, Apache Tomcat) that he wants to install on the remote systems.

2. **Define Tasks in the Playbook**: In the playbook, Josh will define the tasks that need to be performed on the remote systems, like downloading Apache Tomcat, installing it, and configuring it.

3. **Deploy the Playbook**: Once the playbook is complete, Josh deploys it on the **Ansible Master**. 

4. **Execution on Remote Systems**: The playbook is then executed, and Ansible will perform all the tasks defined in the playbook across the target (slave) systems, installing Apache Tomcat on each of them.

### The Power of Ansible Playbooks

Instead of manually logging into each remote system to install the software, Josh can simply write all the necessary instructions in one central location: the Ansible Playbook. By running this playbook from the **Ansible Master**, the software (Apache Tomcat) will be automatically installed on all remote systems defined in the playbook.

### Code File = Playbook

In summary, the **Ansible Playbook** is essentially a code file that contains all the tasks you want to automate on remote systems. By writing the tasks once and running the playbook, you save time and effort, ensuring that the configuration is consistently applied to all target systems.
