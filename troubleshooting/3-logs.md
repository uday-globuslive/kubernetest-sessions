A
 Certainly! Here are some common Kubernetes troubleshooting steps along with example code and multiple samples:

1. Check Pod Status:
   Use the `kubectl get pods` command to check the status of your pods.

   Example:
   ```
   kubectl get pods
   ```

   Sample output:
   ```
   NAME                     READY   STATUS    RESTARTS   AGE
   nginx-6799fc88d8-2nlzx   1/1     Running   0          10m
   redis-7bf6d7b4f4-9j2d8   0/1     Pending   0          5m
   ```

2. Describe Pod:
   Use `kubectl describe pod` to get detailed information about a specific pod.

   Example:
   ```
   kubectl describe pod nginx-6799fc88d8-2nlzx
   ```

   Sample output:
   ```
   Name:         nginx-6799fc88d8-2nlzx
   Namespace:    default
   Priority:     0
   Node:         minikube/192.168.64.2
   Start Time:   Mon, 21 Jun 2023 10:30:00 +0000
   Labels:       app=nginx
                 pod-template-hash=6799fc88d8
   Status:       Running
   IP:           172.17.0.4
   ...
   ```

3. Check Pod Logs:
   Use `kubectl logs` to view the logs of a specific pod.

   Example:
   ```
   kubectl logs nginx-6799fc88d8-2nlzx
   ```

   Sample output:
   ```
   10.244.0.1 - - [21/Jun/2023:10:35:00 +0000] "GET / HTTP/1.1" 200 612 "-" "Mozilla/5.0 ..."
   10.244.0.1 - - [21/Jun/2023:10:35:05 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "-" "Mozilla/5.0 ..."
   ```

4. Check Node Status:
   Use `kubectl get nodes` to check the status of your cluster nodes.

   Example:
   ```
   kubectl get nodes
   ```

   Sample output:
   ```
   NAME       STATUS   ROLES    AGE     VERSION
   minikube   Ready    master   2d3h    v1.20.2
   ```

5. Describe Node:
   Use `kubectl describe node` to get detailed information about a specific node.

   Example:
   ```
   kubectl describe node minikube
   ```

   Sample output:
   ```
   Name:               minikube
   Roles:              master
   Labels:             beta.kubernetes.io/arch=amd64
                       beta.kubernetes.io/os=linux
                       kubernetes.io/arch=amd64
                       kubernetes.io/hostname=minikube
                       kubernetes.io/os=linux
                       minikube.k8s.io/commit=62e108c3dfdec8029a890ad6d8ef96b6461426dc
                       minikube.k8s.io/name=minikube
                       minikube.k8s.io/updated_at=2023_06_21T10_30_00_0700
                       minikube.k8s.io/version=v1.20.0
                       node-role.kubernetes.io/master=
   ...
   ```

6. Check Service Status:
   Use `kubectl get services` to check the status of your services.

   Example:
   ```
   kubectl get services
   ```

   Sample output:
   ```
   NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
   kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   2d3h
   nginx        ClusterIP   10.107.152.94   <none>        80/TCP    1h
   ```

7. Describe Service:
   Use `kubectl describe service` to get detailed information about a specific service.

   Example:
   ```
   kubectl describe service nginx
   ```

   Sample output:
   ```
   Name:              nginx
   Namespace:         default
   Labels:            app=nginx
   Annotations:       <none>
   Selector:          app=nginx
   Type:              ClusterIP
   IP:                10.107.152.94
   Port:              <unset>  80/TCP
   TargetPort:        80/TCP
   Endpoints:         172.17.0.4:80
   Session Affinity:  None
   Events:            <none>
   ```

8. Check Deployments:
   Use `kubectl get deployments` to check the status of your deployments.

   Example:
   ```
   kubectl get deployments
   ```

   Sample output:
   ```
   NAME    READY   UP-TO-DATE   AVAILABLE   AGE
   nginx   1/1     1            1           1h
   redis   0/1     1            0           30m
   ```

9. Describe Deployment:
   Use `kubectl describe deployment` to get detailed information about a specific deployment.

   Example:
   ```
   kubectl describe deployment nginx
   ```

   Sample output:
   ```
   Name:                   nginx
   Namespace:              default
   CreationTimestamp:      Mon, 21 Jun 2023 10:00:00 +0000
   Labels:                 app=nginx
   Annotations:            deployment.kubernetes.io/revision: 1
   Selector:               app=nginx
   Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   ...
   ```
10.Check ConfigMaps and Secrets:

```bash
kubectl get configmaps
kubectl get secrets
kubectl describe configmap <configmap-name>
kubectl describe secret <secret-name>
```

Sample output:
```
NAME               DATA   AGE
nginx-config       2      30m
```

8. Check Persistent Volumes and Claims:

```bash
kubectl get pv
kubectl get pvc
kubectl describe pv <pv-name>
kubectl describe pvc <pvc-name>
```

Sample output:
```
NAME        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS   REASON   AGE
pv-volume   1Gi        RWO            Retain           Bound    default/pvc-claim     manual                  1h
```

9. Check Events:

```bash
kubectl get events
kubectl get events --sort-by=.metadata.creationTimestamp
```

Sample output:
```
LAST SEEN   TYPE     REASON              OBJECT                     MESSAGE
2m          Normal   Scheduled           Pod/nginx-6799fc88d8-8bnj4 Successfully assigned default/nginx-6799fc88d8-8bnj4 to minikube
1m          Normal   Pulled              Pod/nginx-6799fc88d8-8bnj4 Container image "nginx:latest" already present on machine
1m          Normal   Created             Pod/nginx-6799fc88d8-8bnj4 Created container nginx
1m          Normal   Started             Pod/nginx-6799fc88d8-8bnj4 Started container nginx
```

10. Check Resource Usage:

```bash
kubectl top nodes
kubectl top pods
```

Sample output:
```
NAME       CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
minikube   160m         8%     1561Mi          40%       
```

These are just a few examples of Kubernetes troubleshooting steps. Depending on your specific issue, you may need to use different commands or investigate further. Remember to always check the Kubernetes documentation for the most up-to-date and comprehensive information on troubleshooting.
