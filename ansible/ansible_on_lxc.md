Step 1: Update your system
```
sudo apt update
sudo apt upgrade -y
```

Step 2: Install Ansible and SSHPass for Authentication
```
sudo apt install ansible -y
sudo apt install sshpass -y
```

Step 3: Install LXD
LXD is the newer container hypervisor that uses the `lxc` command:
```
sudo snap install lxd
```
We're using snap to install LXD as it's the recommended method and ensures you get the latest version.

Step 4: Initialize LXD
Before creating containers, we need to initialize LXD:
```
sudo lxd init
```
You can accept the default options for this demonstration.

Step 5: Create LXC containers
Now let's create two Ubuntu containers using the new `lxc` command:
```
lxc launch ubuntu:20.04 container1
lxc launch ubuntu:20.04 container2
```
This creates and starts two Ubuntu 20.04 containers in one step.

Step 6: Verify containers are running
```
lxc list
```
This will show you the status and IP addresses of your containers.

Step 7: Install SSH server in containers
```
lxc exec container1 -- apt update
lxc exec container1 -- apt install openssh-server -y
lxc exec container2 -- apt update
lxc exec container2 -- apt install openssh-server -y
```

Step 8: Set root passwords for containers
```
lxc exec container1 -- passwd root
lxc exec container2 -- passwd root
```
Set a simple password like "password" for this demonstration.

Step 9: Enable SSH root login
For this demo, we'll allow root SSH login (not recommended for production):
```
lxc exec container1 -- sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
lxc exec container1 -- systemctl restart sshd
lxc exec container2 -- sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
lxc exec container2 -- systemctl restart sshd
```

And enable  password authentication:
```
lxc exec container1 -- cat /etc/ssh/sshd_config | grep -E '(PasswordAuthentication|PermitRootLogin)'
lxc exec container2 -- cat /etc/ssh/sshd_config | grep -E '(PasswordAuthentication|PermitRootLogin)'
```

1. First, let's clean up the sshd_config file:

   ```
   sudo lxc exec container1 -- sed -i '/^#PasswordAuthentication/d' /etc/ssh/sshd_config
   sudo lxc exec container1 -- sed -i 's/^PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config
   
   sudo lxc exec container2 -- sed -i '/^#PasswordAuthentication/d' /etc/ssh/sshd_config
   sudo lxc exec container2 -- sed -i 's/^PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config
   ```

2. Now, let's make sure root login is explicitly allowed:

   ```
   sudo lxc exec container1 -- sed -i 's/^PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config
   sudo lxc exec container2 -- sed -i 's/^PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config
   ```

3. Restart the SSH service in both containers:

   ```
   sudo lxc exec container1 -- systemctl restart sshd
   sudo lxc exec container2 -- systemctl restart sshd
   ```

4. Now, let's verify the SSH configuration:

   ```
   sudo lxc exec container1 -- grep -E '^(PasswordAuthentication|PermitRootLogin)' /etc/ssh/sshd_config
   sudo lxc exec container2 -- grep -E '^(PasswordAuthentication|PermitRootLogin)' /etc/ssh/sshd_config
   ```

   Both should show `PasswordAuthentication yes` and `PermitRootLogin yes`

2. Let's explicitly set all these options:

   ```
   sudo lxc exec container1 -- bash -c "echo 'PasswordAuthentication yes' >> /etc/ssh/sshd_config"
   sudo lxc exec container1 -- bash -c "echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config"
   sudo lxc exec container1 -- bash -c "echo 'PubkeyAuthentication no' >> /etc/ssh/sshd_config"
   ```

3. Now, let's restart the SSH service:

   ```
   sudo lxc exec container1 -- systemctl restart sshd
   ```

4. Let's also ensure that the root account has a password set:

   ```
   sudo lxc exec container1 -- passwd root
   ```

   Set a password when prompted.


Step 10: Get container IP addresses
```
lxc list
```
Note down the IP addresses; we'll use them in the next step.

Step 11: Create Ansible inventory file
Create a file named `inventory` in your home directory:
```
nano ~/inventory
```
Add the following content (replace IP_ADDRESS1 and IP_ADDRESS2 with the actual IP addresses):
```
[containers]
container1 ansible_host=IP_ADDRESS1
container2 ansible_host=IP_ADDRESS2

[all:vars]
ansible_user=root
ansible_ssh_pass=password
```


This inventory file tells Ansible how to connect to our containers. In a real environment, you'd use SSH keys instead of passwords.

Step 11: Test Ansible connection
Let's verify that Ansible can connect to our containers:

```
ansible all -i ~/inventory -m ping
```

If successful, you should see "pong" responses from both containers.

Instead if you get below error:

```
ansible all -i ~/inventory -m ping
container1 | FAILED! => {
    "msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."
}
container2 | FAILED! => {
    "msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."
}
```

This error is occurring because Ansible is trying to connect to the containers for the first time, and it's encountering the SSH host key verification prompt. Let's resolve this issue:

1. First, let's disable host key checking for Ansible. This is not recommended for production environments, but it's acceptable for our demonstration purposes. Create or edit the Ansible configuration file:

   ```
   nano ~/.ansible.cfg
   ```

   Add these lines to the file:

   ```
   [defaults]
   host_key_checking = False
   ```

   Save and exit the editor.

2. Alternatively, you can set this as an environment variable:

   ```
   export ANSIBLE_HOST_KEY_CHECKING=False
   ```

3. Now, try running the Ansible ping command again:

   ```
   ansible all -i ~/inventory -m ping
   ```

If you're still experiencing issues, let's try to add the host keys manually:

4. For each container, run:

   ```
   ssh-keyscan -H <container_ip> >> ~/.ssh/known_hosts
   ```

   Replace <container_ip> with the actual IP address of each container.

5. After adding the host keys, try the Ansible ping command again:

   ```
   ansible all -i ~/inventory -m ping
   ```

If you're still having trouble, let's verify a few things:

6. Check if you can SSH into the containers manually:

   ```
   ssh root@<container_ip>
   ```

   You should be able to log in with the password you set earlier.

7. Verify the contents of your inventory file:

   ```
   cat ~/inventory
   ```

   Ensure the IP addresses are correct and the format is as we specified earlier.

8. If manual SSH works but Ansible is still failing, try running Ansible with increased verbosity:

   ```
   ansible all -i ~/inventory -m ping -vvv
   ```

   This will provide more detailed output about what's happening during the connection attempt.

9. Lastly, ensure that the SSH service is running in both containers:

   ```
   lxc exec container1 -- systemctl status sshd
   lxc exec container2 -- systemctl status sshd
   ```

   Both should show as active (running).

