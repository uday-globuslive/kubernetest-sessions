In Kubernetes, a service account provides an identity for processes that run in a pod. By default, pods are associated with the default service account in the same namespace. However, you can create custom service accounts to manage permissions and access controls more granularly.

Key Concepts
- Service Account: An account that is used to provide an identity to a process running in a pod. Each namespace has a default service account.
- Role: Defines a set of permissions within a namespace.
- RoleBinding: Grants the permissions defined in a Role to a user or service account within a namespace.
- ClusterRole: Defines a set of permissions at the cluster level.
- ClusterRoleBinding: Grants the permissions defined in a ClusterRole to a user or service account across the entire cluster.

Creating a Service Account
You can create a service account using a YAML file or the kubectl command. Here's an example using YAML:
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: default
```

Assigning Roles to Service Accounts
Next, you'll create a Role and bind it to the service account using a RoleBinding.

Creating a Role
This example Role allows reading pods within the default namespace:
```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

Creating a RoleBinding
Now, bind the pod-reader Role to the my-service-account:

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: ServiceAccount
  name: my-service-account
  namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

Using the Service Account in a Pod
You can specify the service account in your pod definition:

```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: my-service-account
  containers:
  - name: my-container
    image: nginx
```

Verifying the Configuration
Check the service account assigned to the pod:

```
kubectl get pod my-pod -o jsonpath='{.spec.serviceAccountName}'
```

check the permissions.

```
kubectl auth can-i get pods --as=system:serviceaccount:default:my-service-account
```

Breakdown of the Command
kubectl auth can-i: This is the base command used to check if a specific action is allowed.
get pods: This specifies the action you want to check. In this case, it's checking if you can "get" (list or retrieve information about) pods.
--as=system:serviceaccount:default:my-service-account: This flag is used to impersonate a specific user or service account. Here, it impersonates the service account named my-service-account in the default namespace.

Check Permissions Using the Kubernetes API

Inside the pod, you can use the curl command to make an API request to the Kubernetes API server to check permissions. The service account token can be found in /var/run/secrets/kubernetes.io/serviceaccount/token, and the Kubernetes API server can be accessed at https://kubernetes.default.svc.

Here’s an example script to check if the service account can get pods:

```
TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
NAMESPACE=$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace)

curl -s --cacert /var/run/secrets/kubernetes.io/serviceaccount/ca.crt \
  -H "Authorization: Bearer $TOKEN" \
  -X POST \
  -d '{
        "kind": "SelfSubjectAccessReview",
        "apiVersion": "authorization.k8s.io/v1",
        "spec": {
          "resourceAttributes": {
            "namespace": "'$NAMESPACE'",
            "verb": "get",
            "resource": "pods"
          }
        }
      }' \
  https://kubernetes.default.svc/apis/authorization.k8s.io/v1/selfsubjectaccessreviews
```


This command should return yes if the service account has the correct permissions.

ClusterRole and ClusterRoleBinding
For cluster-wide permissions, you can use ClusterRole and ClusterRoleBinding.

Creating a ClusterRole
This example ClusterRole allows reading pods across the entire cluster:

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

Creating a ClusterRoleBinding
Now, bind the cluster-pod-reader ClusterRole to the my-service-account:

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-pods-cluster-wide
subjects:
- kind: ServiceAccount
  name: my-service-account
  namespace: default
roleRef:
  kind: ClusterRole
  name: cluster-pod-reader
  apiGroup: rbac.authorization.k8s.io
```


