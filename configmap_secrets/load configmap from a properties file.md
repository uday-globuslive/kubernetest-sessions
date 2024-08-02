## load configmap from a properties file
To load a ConfigMap in Kubernetes from a properties file, you can use the kubectl create configmap command. Here's a step-by-step guide to create a ConfigMap from a properties file:

- Create a properties file: Prepare your properties file. For example, create a file named app.properties with the following content:
  ```
  key1=value1
  key2=value2
  ```
- Create the ConfigMap: Use the kubectl create configmap command to create a ConfigMap from the properties file.
  ```
  kubectl create configmap my-config --from-file=app.properties
  ```
- Verify the ConfigMap: Check that the ConfigMap was created successfully.
  ```
  kubectl get configmap my-config -o yaml
  ```
- The output should look similar to:
  ```
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: my-config
  data:
    app.properties: |
      key1=value1
      key2=value2
  ```
- Use the ConfigMap in a Pod: You can now reference the ConfigMap in your Pod definition. Here’s an example of how to use the ConfigMap in a Pod:
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: my-pod
  spec:
    containers:
    - name: my-container
      image: my-image
      envFrom:
      - configMapRef:
          name: my-config
  ```
- Alternatively, if you want to mount the properties file as a volume, you can modify your Pod definition as follows:
  ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-pod
    spec:
      containers:
      - name: my-container
        image: my-image
        volumeMounts:
        - name: config-volume
          mountPath: /config
          subPath: app.properties
      volumes:
      - name: config-volume
        configMap:
          name: my-config
          items:
          - key: app.properties
            path: app.properties
    ```
    
    This will mount the `app.properties` file at `/config/app.properties` inside the container.
  
