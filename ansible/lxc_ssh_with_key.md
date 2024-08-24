Here's a step-by-step guide to create an LXC container and set up key-based authentication for SSH:

1. Create a new LXC container:
   ```
   lxc launch ubuntu:20.04 my-container
   ```
   Replace "my-container" with your desired container name.

2. Access the container:
   ```
   lxc exec my-container bash
   ```

3. Update the container and install OpenSSH server:
   ```
   apt update
   apt install openssh-server -y
   ```

4. Exit the container:
   ```
   exit
   ```

5. On your host machine, generate an SSH key pair if you don't already have one:
   ```
   ssh-keygen -t rsa -b 4096
   ```

6. Copy your public key to the container:
   ```
   lxc file push ~/.ssh/id_rsa.pub my-container/root/.ssh/authorized_keys
   ```

7. Set the correct permissions for the .ssh directory and authorized_keys file:
   ```
   lxc exec my-container -- chmod 700 /root/.ssh
   lxc exec my-container -- chmod 600 /root/.ssh/authorized_keys
   ```

8. Configure SSH in the container to use key-based authentication:
   ```
   lxc exec my-container -- sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config
   lxc exec my-container -- sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
   lxc exec my-container -- systemctl restart sshd
   ```

8.1. Or can combine all steps above 2-8 and run step 6 first to copy file and run below bash.
   ```
   #configure.sh
   apt update
   apt install openssh-server -y
   chmod 700 /root/.ssh
   chmod 600 /root/.ssh/authorized_keys
   sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config
   sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
   systemctl restart sshd
   ```
   And run command: ```cat configure.sh | lxc exec container2 bash```
   

9. Get the IP address of your container:
   ```
   lxc list my-container
   ```

10. SSH into your container using the IP address:
    ```
    ssh root@<container-ip>
    ```

You should now be able to SSH into your container using key-based authentication.

Note: This setup uses the root user for SSH access. For better security, you might want to create a non-root user with sudo privileges and use that for SSH access instead. Here's how you can do that:

1. Create a new user in the container:
   ```
   lxc exec my-container -- adduser myuser
   lxc exec my-container -- usermod -aG sudo myuser
   ```

2. Set up the .ssh directory for the new user:
   ```
   lxc exec my-container -- mkdir -p /home/myuser/.ssh
   lxc exec my-container -- cp /root/.ssh/authorized_keys /home/myuser/.ssh/
   lxc exec my-container -- chown -R myuser:myuser /home/myuser/.ssh
   lxc exec my-container -- chmod 700 /home/myuser/.ssh
   lxc exec my-container -- chmod 600 /home/myuser/.ssh/authorized_keys
   ```

3. SSH into the container as the new user:
   ```
   ssh myuser@<container-ip>
   ```

This setup provides a more secure configuration by not allowing direct root SSH access.
