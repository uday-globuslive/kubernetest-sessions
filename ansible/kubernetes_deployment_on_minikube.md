
# Minikube Cluster Setup and Application Deployment using Ansible

## Prerequisites

- **Operating System**: Ubuntu 20.04+ (or any other Linux distribution)
- **Hardware**: At least 2 GB of RAM and 2 CPUs
- **Software**:
  - `VirtualBox` or another hypervisor supported by Minikube
  - `Ansible` installed on your control machine
  - `kubectl` installed for Kubernetes CLI management

## Step 1: Install Minikube

### 1.1. Update Your System

Open a terminal and update your package list:

```bash
sudo apt-get update
```

### 1.2. Install Dependencies

Ensure you have the required dependencies:

```bash
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
```

### 1.3. Install VirtualBox

If you don't have a hypervisor, install VirtualBox:

```bash
sudo apt-get install -y virtualbox
```

## Step 2: Install Minikube

1. Download Minikube:
   ```bash
   curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
   ```

2. Install Minikube:
   ```bash
   sudo install minikube-linux-amd64 /usr/local/bin/minikube
   ```

3. Start Minikube:
   ```bash
   minikube start
   ```

4. Verify Minikube is running:
   ```bash
   minikube status
   ```

## Step 3: Install kubectl

1. Download kubectl:
   ```bash
   curl -LO "https://dl.k8s.io/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
   ```

2. Install kubectl:
   ```bash
   chmod +x kubectl
   sudo mv kubectl /usr/local/bin/
   ```

3. Verify kubectl installation:
   ```bash
   kubectl version --client
   ```

## Step 4: Install Ansible

1. Add the Ansible PPA:
   ```bash
   sudo add-apt-repository --yes --update ppa:ansible/ansible
   ```

2. Install Ansible:
   ```bash
   sudo apt install ansible
   ```

3. Verify Ansible installation:
   ```bash
   ansible --version
   ```

## Step 5: Deploy an Application using Ansible

1. Create an Ansible Playbook to deploy an application:

   ```yaml
   ---
   - hosts: localhost
     tasks:
       - name: Create Kubernetes deployment
         kubernetes.core.k8s:
           state: present
           definition:
             apiVersion: apps/v1
             kind: Deployment
             metadata:
               name: my-app
             spec:
               replicas: 2
               selector:
                 matchLabels:
                   app: my-app
               template:
                 metadata:
                   labels:
                     app: my-app
                 spec:
                   containers:
                   - name: my-app
                     image: nginx
                     ports:
                     - containerPort: 80
   ```

2. Run the Ansible Playbook:

   ```bash
   ansible-playbook deploy-app.yml
   ```

3. Verify the deployment:

   ```bash
   kubectl get deployments
   kubectl get pods
   ```

## Step 6: Access the Application

1. Expose the deployment using a service:

   ```bash
   kubectl expose deployment my-app --type=NodePort --port=80
   ```

2. Get the URL to access the application:

   ```bash
   minikube service my-app --url
   ```

## Conclusion

You have successfully set up a Minikube cluster, deployed an application using Ansible, and accessed it.
