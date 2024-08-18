I'll guide you through creating a Kubernetes multi-node cluster using LXC containers, assuming you're a complete beginner. This process involves several steps, so let's break it down:

Prerequisites:
- A Linux machine (preferably Ubuntu) with sudo access
- Internet connection

Step 1: Install LXC

1. Update your system:
   ```
   sudo apt update && sudo apt upgrade -y
   ```

2. Install LXC:
   ```
   sudo apt install lxc lxc-utils -y
   ```

Step 2: Create LXC containers

1. Create three containers (one master and two workers):
   ```
   sudo lxc-create -n k8s-master -t download -- --dist ubuntu --release focal --arch amd64
   sudo lxc-create -n k8s-worker1 -t download -- --dist ubuntu --release focal --arch amd64
   sudo lxc-create -n k8s-worker2 -t download -- --dist ubuntu --release focal --arch amd64
   ```

2. Start the containers:
   ```
   sudo lxc-start -n k8s-master
   sudo lxc-start -n k8s-worker1
   sudo lxc-start -n k8s-worker2
   ```

Step 3: Configure the containers

1. Access each container (repeat for all three):
   ```
   sudo lxc-attach -n k8s-master
   ```

2. Inside each container, update and install necessary packages:
   ```
   apt update && apt upgrade -y
   apt install curl apt-transport-https ca-certificates software-properties-common -y
   ```

3. Add Kubernetes repository and install kubeadm, kubelet, and kubectl:
   ```
   curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
   echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
   apt update
   apt install kubeadm kubelet kubectl -y
   ```

4. Install Docker:
   ```
   apt install docker.io -y
   systemctl enable docker
   systemctl start docker
   ```

5. Exit the container:
   ```
   exit
   ```

Step 4: Initialize the master node

1. Access the master container:
   ```
   sudo lxc-attach -n k8s-master
   ```

2. Initialize the Kubernetes cluster:
   ```
   kubeadm init --pod-network-cidr=10.244.0.0/16
   ```

3. Set up kubectl:
   ```
   mkdir -p $HOME/.kube
   cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   chown $(id -u):$(id -g) $HOME/.kube/config
   ```

4. Install a pod network (we'll use Flannel):
   ```
   kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
   ```

5. Note the join command output from step 2. You'll need this for the worker nodes.

Step 5: Join worker nodes

1. Access each worker container:
   ```
   sudo lxc-attach -n k8s-worker1
   ```

2. Run the join command you noted from the master node initialization. It will look something like this:
   ```
   kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash <hash>
   ```

3. Repeat for the second worker node.

Step 6: Verify the cluster

1. On the master node, check the status of the nodes:
   ```
   kubectl get nodes
   ```
