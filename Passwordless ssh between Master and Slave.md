# Enable Keyless SSH Access Between Ansible Master and Slave

To enable keyless SSH access between your Ansible master (control node) and slave (managed node), you need to configure SSH key-based authentication. This allows you to run Ansible commands without needing to input a password each time. Here's how you can do it step by step:

---

## Step 1: Generate an SSH Key Pair on the Ansible Master (Control Node)

1. Open a terminal on your Ansible master (the control node).
2. Generate an SSH key pair by running:

   ```bash
   ssh-keygen -t rsa -b 2048
````

* This will prompt you to choose the location to save the key. Press Enter to accept the default path (`~/.ssh/id_rsa`).
* You can choose to enter a passphrase or leave it empty (for keyless login, leave it empty).
* This will generate two files:

  * **Private key**: `~/.ssh/id_rsa`
  * **Public key**: `~/.ssh/id_rsa.pub`

---

## Step 2: Copy the Public Key to the Managed Node (Slave)

### Option 1: Using `ssh-copy-id`

Copy the public key from the Ansible master to the slave node using `ssh-copy-id`:

```bash
ssh-copy-id user@slave_ip
```

* Replace `user` with the appropriate username on the slave machine and `slave_ip` with the slave node's IP address.
* You will be prompted for the password of the remote user. Enter it once, and `ssh-copy-id` will copy your public key (`~/.ssh/id_rsa.pub`) to the slave machine’s `~/.ssh/authorized_keys`.

### Option 2: Manually Copy the Key

If `ssh-copy-id` is not available:

1. On the Ansible master, display the content of your public key:

   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

2. On the slave node, create the `.ssh` directory if it doesn't already exist:

   ```bash
   mkdir -p ~/.ssh
   ```

3. Paste the contents of the public key into a file named `authorized_keys` on the slave node:

   ```bash
   echo "your-public-key" >> ~/.ssh/authorized_keys
   ```

4. Make sure the permissions are set correctly:

   ```bash
   chmod 600 ~/.ssh/authorized_keys
   chmod 700 ~/.ssh
   ```

---

## Step 3: Verify SSH Access from the Master to the Slave

1. From the Ansible master, try to SSH into the slave without a password:

   ```bash
   ssh user@slave_ip
   ```

   You should be able to log in without being asked for a password.

---

## Step 4: Configure Ansible to Use SSH

1. On the Ansible master, make sure that the `ansible_ssh_private_key_file` option is set in your Ansible inventory file or playbook.

   ### Example in `inventory.ini`:

   ```ini
   [web]
   slave_ip ansible_ssh_user=user ansible_ssh_private_key_file=~/.ssh/id_rsa
   ```

   ### Example in a playbook:

   ```yaml
   ---
   - name: Ensure keyless SSH works
     hosts: web
     tasks:
       - name: Ping the node
         ping:
   ```

---

## Step 5: Test the Connection from Ansible Master to Slave

1. Test Ansible by running a simple `ping` module command:

   ```bash
   ansible web -m ping
   ```

   * Replace `web` with the appropriate group or host name from your inventory file.
   * If everything is configured correctly, you should see a response like:

     ```bash
     slave_ip | SUCCESS | rc=0 >>
     pong
     ```

---

## Additional Tips

* If you're using a non-standard SSH key or a custom location for the key, ensure that the key is properly referenced in your Ansible configuration or inventory.
* If the managed node is behind a firewall, ensure that port **22 (SSH)** is open for incoming connections.
* You can also use `ansible.cfg` to set global SSH options:

  ```ini
  [defaults]
  private_key_file = ~/.ssh/id_rsa
  ```

---

That’s it! You've successfully enabled **keyless SSH access** between the Ansible master and slave. Now, you can run your Ansible commands without having to manually input a password each time.

