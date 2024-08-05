## Users
In Kubernetes, users are entities that interact with the cluster. Users can be human users or machine users (such as CI/CD systems). Kubernetes itself does not manage users. Instead, user management is handled externally (e.g., through an identity provider).

### Example:

- A developer with access to a Kubernetes cluster.
- A CI/CD system that deploys applications to the cluster.

## Service Accounts
Service accounts are a way for pods to authenticate themselves within a Kubernetes cluster. Unlike users, service accounts are managed by Kubernetes and are used by applications running in pods to interact with the Kubernetes API.

### Example:
```YAML
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: default
```

## Role
A Role in Kubernetes defines a set of permissions within a namespace. Roles are namespaced, meaning they only apply to resources within that namespace.

### Example:
```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

## ClusterRole
A ClusterRole is similar to a Role, but it applies to the entire cluster. ClusterRoles can be used to grant permissions across all namespaces or to non-namespaced resources.

### Example:
```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admin
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "list"]
```

## RoleBinding
A RoleBinding assigns a Role to a user, group, or service account within a specific namespace. It grants the permissions defined in the Role to the specified subjects.

### Example:
```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: "jane"
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

## ClusterRoleBinding
A ClusterRoleBinding assigns a ClusterRole to a user, group, or service account at the cluster level. It grants the permissions defined in the ClusterRole to the specified subjects across the entire cluster.

### Example
```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-binding
subjects:
- kind: User
  name: "john"
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

Summary
- Users: Human or machine entities interacting with the cluster.
- Service Accounts: Managed by Kubernetes, used by pods to authenticate with the API.
- Role: Defines a set of permissions within a namespace.
- ClusterRole: Defines a set of permissions cluster-wide.
- RoleBinding: Assigns a Role to a user, group, or service account within a namespace.
- ClusterRoleBinding: Assigns a ClusterRole to a user, group, or service account cluster-wide.
