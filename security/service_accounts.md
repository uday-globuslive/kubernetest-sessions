## You can use serviceAccountName to specify custom service account token as part of Pod.

```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: custom-token
```
