
# Minikube Cluster Setup and Application Deployment using Ansible on Windows

## Prerequisites

- **Operating System**: Windows 10/11
- **Hardware**: At least 2 GB of RAM and 2 CPUs
- **Software**:
  - `Hyper-V` or another hypervisor supported by Minikube
  - `Ansible` installed on your control machine (WSL recommended)
  - `kubectl` installed for Kubernetes CLI management (WSL recommended)

## Step 1: Install Minikube

### 1.1. Enable Hyper-V (if using Hyper-V)

1. Open PowerShell as Administrator.
2. Run the following command to enable Hyper-V:
   ```powershell
   Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
   ```
3. Restart your computer.

### 1.2. Install Minikube

1. Download the Minikube installer for Windows from the official website: [Minikube Releases](https://github.com/kubernetes/minikube/releases/latest)
2. Run the installer and follow the prompts.
3. Add Minikube to your system's `PATH` during installation.

### 1.3. Start Minikube

1. Open a new Command Prompt or PowerShell window.
2. Start Minikube:
   ```powershell
   minikube start --driver=hyperv
   ```
   or, if you are using another hypervisor:
   ```powershell
   minikube start --driver=virtualbox
   ```

3. Verify Minikube is running:
   ```powershell
   minikube status
   ```

## Step 2: Install kubectl

1. Download kubectl for Windows from the official Kubernetes website: [kubectl Releases](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
2. Add the downloaded `kubectl` binary to your system's `PATH`.

3. Verify kubectl installation:
   ```powershell
   kubectl version --client
   ```

## Step 3: Install Ansible on Windows

### Option 1: Using Windows Subsystem for Linux (WSL)

1. Install WSL and set up a Linux distribution (e.g., Ubuntu) from the Microsoft Store.
2. Open your WSL terminal and update the package list:
   ```bash
   sudo apt-get update
   ```

3. Add the Ansible PPA and install Ansible:
   ```bash
   sudo apt-add-repository --yes --update ppa:ansible/ansible
   sudo apt-get install ansible
   ```

4. Verify Ansible installation:
   ```bash
   ansible --version
   ```

### Option 2: Using Cygwin

1. Download and install Cygwin from the [official website](https://www.cygwin.com/).
2. During installation, select `ansible` from the package list.
3. Complete the installation and verify Ansible installation using:
   ```bash
   ansible --version
   ```

## Step 4: Deploy an Application using Ansible

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

## Step 5: Access the Application

1. Expose the deployment using a service:

   ```bash
   kubectl expose deployment my-app --type=NodePort --port=80
   ```

2. Get the URL to access the application:

   ```bash
   minikube service my-app --url
   ```

## Conclusion

You have successfully set up a Minikube cluster on Windows, deployed an application using Ansible, and accessed it.
