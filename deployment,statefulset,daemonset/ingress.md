### Demo on minikube

### Deploying a sample application and configuring an NGINX Ingress Controller in Kubernetes involves several steps. Below is a step-by-step guide.

Step 1: Set Up Kubernetes Cluster
Ensure you have a running Kubernetes cluster. If you don't have one, you can create one using tools like Minikube, kind, or on cloud providers like AWS (using EKS), GCP (using GKE), or Azure (using AKS).

Step 2: Install NGINX Ingress Controller
The NGINX Ingress Controller is a popular choice for managing ingress traffic to your applications in Kubernetes.

  - Add the NGINX Ingress Helm repository:
    ```
    helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
    helm repo update
    ```
  - Install the NGINX Ingress Controller:
    ```
    helm install nginx-ingress ingress-nginx/ingress-nginx --set controller.publishService.enabled=true
    ```
    This command installs the NGINX Ingress Controller in your cluster. The publishService.enabled=true option is useful when your cluster has an external load balancer, ensuring that the correct external IP is used.
  - Verify the Installation:
    ```
    kubectl get pods -n default -l app.kubernetes.io/name=ingress-nginx
    kubectl get svc -n default -l app.kubernetes.io/name=ingress-nginx
    ```
  - The nginx-ingress service should have an external IP if your cluster supports LoadBalancer services.

Step 3: Deploy a Sample Application
Let's deploy a simple application, like hello-world, in your Kubernetes cluster.
  - Create a deployment: ```kubectl create deployment hello-world --image=gcr.io/google-samples/hello-app:1.0```
  - Expose the Deployment: ```kubectl expose deployment hello-world --port=80 --target-port=8080```.
    This command creates a Service of type ClusterIP that exposes the hello-world application on port 80 inside the cluster.
Step 4: Configure the Ingress Resource
Now, create an Ingress resource to route external traffic to your sample application using the NGINX Ingress Controller.
  - Create an Ingress YAML file (ingress.yaml):
    ```
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: hello-world-ingress
      annotations:
        nginx.ingress.kubernetes.io/rewrite-target: /
    spec:
      rules:
      - host: <your-domain-or-ip>
        http:
          paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: hello-world
                port:
                  number: 80
    ```
  - Apply the Ingress Resource: ```kubectl apply -f ingress.yaml```
  - Verify the Ingress Configuration: ```kubectl get ingress```
    Ensure that the ingress resource is created and that the external IP or hostname is associated with it.

Step 5: Access Your Application
  - If you have a domain name, configure it to point to the external IP of your NGINX Ingress Controller.
  - If you are using Minikube, use the following command to get the NGINX IP: ```minikube ip```
  - Access the application by visiting http://<your-domain-or-ip>/ in your browser.

Step 6: Clean Up Resources
Once you are done testing, you can clean up the resources:
```
kubectl delete ingress hello-world-ingress
kubectl delete svc hello-world
kubectl delete deployment hello-world
helm uninstall nginx-ingress
```
This process will remove the Ingress, Service, Deployment, and the NGINX Ingress Controller from your cluster.


 


