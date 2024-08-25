Below steps will guide you through the process of creating a Kubernetes cluster using kubeadm on 4 Ubuntu 24 nodes, creating a sample deployment and daemonset, and configuring a jumpbox with kubectl. I'll explain each step in detail.

Prerequisites:
- 4 Ubuntu 24 nodes (1 master, 3 workers)
- 1 jumpbox (for remote management)
- All nodes have static IP addresses and can communicate with each other

Step 1: Prepare all nodes

On all 4 nodes, perform the following steps:

1.1. Update the system:
```
sudo apt update && sudo apt upgrade -y
```

1.2. Install required packages:
```
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
```

1.3. Add Kubernetes repository:
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

1.4. Update package list:
```
sudo apt update
```

1.5. Install Docker:
```
sudo apt install -y docker.io
```

1.6. Install kubeadm, kubelet, and kubectl:
```
sudo apt install -y kubeadm kubelet kubectl
```

1.7. Hold these packages at their current version:
```
sudo apt-mark hold kubeadm kubelet kubectl
```

1.8. Enable and start Docker:
```
sudo systemctl enable docker
sudo systemctl start docker
```

1.9. Disable swap:
```
sudo swapoff -a
```

1.10. Edit /etc/fstab to comment out any swap entries:
```
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

Step 2: Initialize the master node

On the master node:

2.1. Initialize the cluster:
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

2.2. Set up kubectl for the root user:
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

2.3. Install a pod network add-on (Flannel in this example):
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

2.4. Get the join command for worker nodes:
```
kubeadm token create --print-join-command
```

Step 3: Join worker nodes to the cluster

On each worker node, run the join command obtained from step 2.4:

```
sudo kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash <hash>
```

Step 4: Verify cluster status

On the master node:

4.1. Check node status:
```
kubectl get nodes
```

4.2. Check pod status:
```
kubectl get pods --all-namespaces
```

Step 5: Create a sample deployment

On the master node:

5.1. Create a deployment YAML file (e.g., nginx-deployment.yaml):
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

5.2. Apply the deployment:
```
kubectl apply -f nginx-deployment.yaml
```

5.3. Verify the deployment:
```
kubectl get deployments
kubectl get pods
```

Step 6: Create a sample DaemonSet

On the master node:

6.1. Create a DaemonSet YAML file (e.g., fluentd-daemonset.yaml):
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-daemonset
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-logging
  template:
    metadata:
      labels:
        name: fluentd-logging
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:v1-debian-elasticsearch
```

6.2. Apply the DaemonSet:
```
kubectl apply -f fluentd-daemonset.yaml
```

6.3. Verify the DaemonSet:
```
kubectl get daemonsets -n kube-system
kubectl get pods -n kube-system
```

Step 7: Configure jumpbox with kubectl

On the jumpbox:

7.1. Install kubectl:
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

7.2. Copy the kubeconfig file from the master node:
```
scp user@master-node:/etc/kubernetes/admin.conf ~/.kube/config
```

7.3. Test kubectl connection:
```
kubectl get nodes
```

Explanation of tools:

1. kubeadm: A tool for bootstrapping a Kubernetes cluster. It handles the initial setup of the control plane components and configuring the cluster.

2. kubectl: The command-line tool for interacting with Kubernetes clusters. It allows you to deploy applications, inspect and manage cluster resources, and view logs.

3. kubelet: The primary node agent that runs on each node in the cluster. It ensures that containers are running in a Pod.

By following these steps, you'll have a functioning 4-node Kubernetes cluster with a sample deployment and DaemonSet, as well as a configured jumpbox for remote management using kubectl.
