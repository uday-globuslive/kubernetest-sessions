## create deployment using ad-hoc command.
```
kubectl create deployment my-deployment --image=your-docker-hub-repo/your-image-name --replicas=3 --port=8080
```

## to create a service as loadbalancer type.
```
kubectl expose deployment my-deployment --port=8080 --target-port=80 --type=LoadBalancer
```

## to create a service as node port service
```
kubectl expose deployment my-deployment --port=8080 --target-port=80 --type=NodePort
```

## to create nodePort service with custom name different than deployment.
```
kubectl expose deployment my-deployment --port=8080 --target-port=80 --type=NodePort --name=my-service
```


### to fix the NodePort port number.

- using yaml
    ```
  apiVersion: v1
  kind: Service
  metadata:
    name: my-service
  spec:
    selector:
      app: my-deployment
    type: NodePort
    ports:
      - protocol: TCP
        port: 8080
        targetPort: 80
        nodePort: 30000
   ```
    And then `kubectl apply -f service.yml`
- to create using adhoc comand
  ```
  kubectl expose deployment my-deployment --port=8080 --target-port=80 --type=NodePort --name=my-service --external-ip=<your-external-ip> --node-port=30000
  ```

Replace <your-external-ip> with the actual external IP address, or use --external-ip=NODE_IP to select the IP address of the node automatically.

In the above command, we specify the deployment my-deployment to expose. The --port flag sets the listening port of the service (8080), and the --target-port flag sets the target port of the pods (80). The --type=NodePort flag indicates that the service should be of type NodePort.

The --name=my-service flag sets the name of the service to my-service, and the --external-ip flag assigns the external IP address. Additionally, we set the --node-port flag to explicitly define the desired port number as 30000.

After executing the command, you can use kubectl get services to verify the creation of the service and ensure that it has been assigned the desired port number.
