In Kubernetes, ConfigMap and Secrets are used to manage configuration data for your applications. Both provide a way to decouple environment-specific configurations from your container images, allowing you to keep your container images portable and the configurations easy to change without rebuilding the images.

## ConfigMap
ConfigMap is an API object used to store non-confidential data in key-value pairs. ConfigMaps can be used to store configuration files, environment variables, command-line arguments, and more.

### Example of Creating and Using a ConfigMap
- Create a ConfigMap: You can create a ConfigMap from a file, a literal value, or a directory. Here’s how to create a ConfigMap from a file:
  ```
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: my-config
  data:
    database_url: "mongodb://db.example.com:27017"
    log_level: "DEBUG"
  ```
- Save the above YAML as configmap.yaml and create the ConfigMap:
  ```
  kubectl apply -f configmap.yaml
  ```
- You can use the ConfigMap in a Pod as environment variables or mount it as a volume.
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: my-pod
  spec:
    containers:
    - name: my-container
      image: my-image
      env:
      - name: DATABASE_URL
        valueFrom:
          configMapKeyRef:
            name: my-config
            key: database_url
      - name: LOG_LEVEL
        valueFrom:
          configMapKeyRef:
            name: my-config
            key: log_level
  ```
