
# Installing Ansible on AWS

## Pre-requisites

### Step 1: Ensure Python is Installed on Both Machines (Master and Slave)
By default, AWS Ubuntu images have Python installed. You can skip this step if you are setting up Ansible on AWS. On other machines, install Python using the following command:
```bash
sudo apt-get install python3
```

### Step 2: Enable Keyless SSH Access Between Ansible Master and Slave
To enable keyless SSH access, follow these steps:

1. On the Master machine, generate an SSH key pair:
    ```bash
    ssh-keygen -t rsa
    ```
    Press `Enter` to accept the default values until you reach the screen where the key pair is generated.

### Step 3: Print the Public Key (id_rsa.pub)
After generating the key pair, print the public key with the following command:
```bash
sudo cat ./.ssh/id_rsa.pub
```

### Step 4: Copy the Public Key to the Slave’s `authorized_keys` File
Copy the output from the above command and paste it into the slave machine's `authorized_keys` file. Use the following command on the slave machine:
```bash
sudo nano ./.ssh/authorized_keys
```
Paste the public key into the second line of the file. Save and exit the editor.

Now, keyless access has been configured between your Ansible Master and Slave. You can verify it by SSH-ing from your Master to your Slave and checking that the connection is successful.

> ✅ Manually test SSH from master to slave
> Run the following command from the Ansible master node:

> ```bash
> ssh username@slave-ip
> ```
> You should:

> Connect without being asked for a password (if using key-based authentication)

> Get shell access to the remote machine

> If this works, Ansible should be able to use SSH to connect too.

With this, the pre-requisites are successfully configured.

---

## Installing Ansible on the Master

### Step 1: Install Ansible on the Master Machine
Run the following commands to install Ansible on the Master machine:
```bash
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

### Step 2: Configure the Slave by Creating the Hosts File
Create a new hosts file for the Ansible configuration. Edit the `/etc/ansible/hosts` file with the following command:
```bash
sudo nano /etc/ansible/hosts
```

Inside the file, add the following syntax to define the slave. Replace `<slave-ip-address>` with the actual IP address of the slave:
```
[servers]
server1 ansible_host=<slave-private-ip-address>
```
You can ignore the sample entries in the file and add the new values as required. This will complete the Ansible configuration on the Master.

### Step 3: Test the Ansible Master-Slave Connection
Finally, test the connection between the Master and Slave by running the following command:
```bash
ansible -m ping all
```

If everything is configured correctly, you should receive a successful ping response from the slave.

