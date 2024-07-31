To create a ConfigMap in Kubernetes that holds multiple database URLs in an array structure, you can follow these steps. Below is an example of a ConfigMap in YAML format:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: database-config
  namespace: default
data:
  # Each entry in the array is a key-value pair where the key is the array index
  DATABASE_URLS: |-
    [
      "postgres://user:password@db1.example.com:5432/database1",
      "postgres://user:password@db2.example.com:5432/database2",
      "postgres://user:password@db3.example.com:5432/database3"
    ]
```

- Explanation
  - apiVersion: Specifies the API version (v1).
  - kind: Specifies the type of Kubernetes object (ConfigMap).  
  - metadata: Provides metadata about the ConfigMap, such as its name and namespace.
  - data: Contains the key-value pairs. Here, we define a single key DATABASE_URLS with its value as a JSON array of URLs.

## Accessing the ConfigMap in a Pod
To access these database URLs in a Pod, you can use environment variables or volume mounts.

### Using Environment Variables
You can inject the database URLs into a container as environment variables.
```
apiVersion: v1
kind: Pod
metadata:
  name: database-client
  namespace: default
spec:
  containers:
    - name: client-container
      image: your-database-client-image
      env:
        - name: DATABASE_URLS
          valueFrom:
            configMapKeyRef:
              name: database-config
              key: DATABASE_URLS
```

Using Volume Mounts
Alternatively, you can mount the ConfigMap as a volume.

```
apiVersion: v1
kind: Pod
metadata:
  name: database-client
  namespace: default
spec:
  containers:
    - name: client-container
      image: your-database-client-image
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: database-config
```

In your application, you can then read the contents from the file at /etc/config/DATABASE_URLS.

This setup ensures that you can manage multiple database URLs efficiently using a ConfigMap in Kubernetes.
