To create a Kubernetes ConfigMap that contains multiple database URLs in an array structure, you can define the ConfigMap in a YAML file. Here is an example:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: database-config
  namespace: your-namespace
data:
  DB_URLS: |
    - "postgresql://user:password@db1.example.com:5432/dbname"
    - "mysql://user:password@db2.example.com:3306/dbname"
    - "mongodb://user:password@db3.example.com:27017/dbname"
```

In this example, the DB_URLS key contains a list of database URLs in a YAML array format. This allows your application to read and parse the ConfigMap data easily.

To use this ConfigMap in a Pod, you can reference it in your Pod's configuration. Here is an example of how to do this:

```
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
  namespace: your-namespace
spec:
  containers:
    - name: example-container
      image: your-image
      env:
        - name: DB_URLS
          valueFrom:
            configMapKeyRef:
              name: database-config
              key: DB_URLS
```
In this example, the environment variable DB_URLS in the container is set to the value of DB_URLS from the ConfigMap. Your application can then read the DB_URLS environment variable and parse it as needed.

If you prefer to mount the ConfigMap as a file instead of using environment variables, you can do it like this:

```
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
  namespace: your-namespace
spec:
  containers:
    - name: example-container
      image: your-image
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: database-config
```

This will mount the entire ConfigMap as files under the /etc/config directory in the container. The DB_URLS key will be available as a file named /etc/config/DB_URLS, and your application can read the file to get the database URLs.
