Secrets are similar to ConfigMaps but are intended to hold confidential data such as passwords, OAuth tokens, and SSH keys. Secrets are encoded in Base64 and stored securely.

### Example of Creating and Using a Secret

- Create a Secret: You can create a Secret from a file, a literal value, or using kubectl. Here’s how to create a Secret from literal values:
  ```
  kubectl create secret generic my-secret \
  --from-literal=username=myuser \
  --from-literal=password=mypassword
  ```
- Use the Secret in a Pod: You can use the Secret in a Pod as environment variables or mount it as a volume.
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
      - name: USERNAME
        valueFrom:
          secretKeyRef:
            name: my-secret
            key: username
      - name: PASSWORD
        valueFrom:
          secretKeyRef:
            name: my-secret
            key: password

  ```
##Key Differences
- Purpose:
  - ConfigMap: Store non-confidential configuration data.
  - Secret: Store confidential data.
- Data Encoding:
  - ConfigMap: Plain text.
  - Secret: Base64-encoded (though not encrypted by default).
- Security:
  - ConfigMap: No built-in security.
  - Secret: Treated specially by Kubernetes and can be encrypted at rest (requires additional configuration).
- Usage:
  - Both ConfigMaps and Secrets can be used as environment variables, command-line arguments, or mounted as volumes in pods.

Example Comparison
Here is an example showing both ConfigMap and Secret usage in the same Pod:

```
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: example-image
    env:
    - name: CONFIG_LOG_LEVEL
      valueFrom:
        configMapKeyRef:
          name: my-config
          key: log_level
    - name: SECRET_USERNAME
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: username
    - name: SECRET_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: password
```

This Pod will have environment variables sourced from both a ConfigMap and a Secret, demonstrating how you can manage both configuration and sensitive data efficiently in Kubernetes.


  
