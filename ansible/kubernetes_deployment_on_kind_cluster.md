
# Kind Cluster Setup and Application Deployment using Ansible on Ubuntu

## Prerequisites

- **Operating System**: Ubuntu 20.04+ (or any other Linux distribution)
- **Hardware**: At least 2 GB of RAM and 2 CPUs
- **Software**:
  - `Docker` installed on your system
  - `Ansible` installed on the same Ubuntu VM
  - `kubectl` installed for Kubernetes CLI management

## Step 1: Install Docker

1. Update your package list:
   ```bash
   sudo apt-get update
   ```

2. Install required packages:
   ```bash
   sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
   ```

3. Add Docker's official GPG key:
   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. Add the Docker repository:
   ```bash
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```

5. Install Docker:
   ```bash
   sudo apt-get update
   sudo apt-get install -y docker-ce
   ```

6. Verify Docker installation:
   ```bash
   sudo systemctl status docker
   ```

7. Add your user to the `docker` group to run Docker without `sudo`:
   ```bash
   sudo usermod -aG docker $USER
   ```

   Log out and log back in for the changes to take effect.

## Step 2: Install Kind

1. Download the latest Kind binary:
   ```bash
   curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
   ```

2. Make the Kind binary executable and move it to a directory in your `PATH`:
   ```bash
   chmod +x ./kind
   sudo mv ./kind /usr/local/bin/kind
   ```

3. Verify Kind installation:
   ```bash
   kind --version
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

## Step 4: Create a Kind Cluster

1. Create a Kind configuration file `kind-config.yaml`:

   ```yaml
   kind: Cluster
   apiVersion: kind.x-k8s.io/v1alpha4
   nodes:
     - role: control-plane
     - role: worker
     - role: worker
   ```

2. Create a Kind cluster using the configuration file:
   ```bash
   kind create cluster --config kind-config.yaml
   ```

3. Verify the cluster is running:
   ```bash
   kubectl get nodes
   ```

## Step 5: Install Ansible

1. Add the Ansible PPA:
   ```bash
   sudo apt-add-repository --yes --update ppa:ansible/ansible
   ```

2. Install Ansible:
   ```bash
   sudo apt-get install ansible
   ```

3. Verify Ansible installation:
   ```bash
   ansible --version
   ```

## Step 6: Deploy an Application using Ansible

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

## Step 7: Access the Application

1. Expose the deployment using a service:

   ```bash
   kubectl expose deployment my-app --type=NodePort --port=80
   ```

2. Get the URL to access the application:

   ```bash
   kubectl get svc
   ```

   Then, access the application using the worker node's IP and the NodePort.

## Conclusion

You have successfully set up a Kind cluster on Ubuntu, deployed an application using Ansible, and accessed it.
