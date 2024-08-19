# Kubernetes Scaling and Custom Resources

In this README, we'll explore some important concepts in Kubernetes, including Horizontal Pod Autoscaler (HPA), Vertical Pod Autoscaler (VPA), Endpoints, EndpointSlices, and Custom Resource Definitions (CRDs).

## Table of Contents
1. [Horizontal Pod Autoscaler (HPA)](#horizontal-pod-autoscaler-hpa)
2. [Vertical Pod Autoscaler (VPA)](#vertical-pod-autoscaler-vpa)
3. [Endpoints](#endpoints)
4. [EndpointSlices](#endpointslices)
5. [Custom Resource Definitions (CRDs)](#custom-resource-definitions-crds)

## Horizontal Pod Autoscaler (HPA)

### What is HPA?
Horizontal Pod Autoscaler (HPA) is a Kubernetes feature that automatically adjusts the number of pods in a deployment, replication controller, or replica set based on observed CPU utilization or custom metrics.

### When is HPA required?
HPA is useful when you want to automatically scale the number of pods based on demand. This is particularly helpful for applications with varying workloads.

### Example
Imagine you have a web application that experiences high traffic during business hours and low traffic at night. HPA can automatically increase the number of pods during peak hours and decrease them during off-hours.

```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: my-web-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-web-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 50
```

In this example, the HPA will maintain between 2 and 10 replicas of the `my-web-app` deployment, aiming to keep the average CPU utilization at 50%.

## Vertical Pod Autoscaler (VPA)

### What is VPA?
Vertical Pod Autoscaler (VPA) automatically adjusts the CPU and memory reservations for your pods to help "right size" your applications.

### When is VPA required?
VPA is useful when you want to automatically adjust the resource requests and limits of your containers based on their actual usage. This helps in efficient resource allocation and can reduce costs.

### Example
Let's say you have a database pod that sometimes needs more memory than initially allocated. VPA can automatically adjust its memory reservation based on observed usage.

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-database-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind: Deployment
    name: my-database
  updatePolicy:
    updateMode: "Auto"
```

This VPA will automatically adjust the CPU and memory requests for the `my-database` deployment based on observed usage.

## Endpoints

### What are Endpoints?
Endpoints in Kubernetes are a way to keep track of the IP addresses of pods that back a Service.

### When are Endpoints used?
Endpoints are automatically created and managed by Kubernetes when you create a Service. They're used by the kube-proxy to direct traffic to the appropriate pods.

### Example
When you create a Service, Kubernetes automatically creates an Endpoints object with the same name:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

Kubernetes will automatically create and manage an Endpoints object named `my-service` that lists the IP addresses of the pods selected by the Service.

## EndpointSlices

### What are EndpointSlices?
EndpointSlices are a more scalable alternative to Endpoints. They provide a way to track IP addresses, ports, readiness, and topology information for a larger number of backends.

### When are EndpointSlices used?
EndpointSlices are particularly useful in large clusters with many Services and Endpoints. They improve performance and scalability by allowing the endpoint information to be split across multiple objects.

### Example
EndpointSlices are automatically created by Kubernetes when you create a Service, similar to Endpoints:

```yaml
apiVersion: discovery.k8s.io/v1
kind: EndpointSlice
metadata:
  name: my-service-abc
  labels:
    kubernetes.io/service-name: my-service
addressType: IPv4
ports:
  - name: http
    protocol: TCP
    port: 80
endpoints:
  - addresses:
      - "10.1.2.3"
    conditions:
      ready: true
    hostname: pod-1
    topology:
      kubernetes.io/hostname: node-1
```

This EndpointSlice represents a subset of the endpoints for the `my-service` Service.

## Custom Resource Definitions (CRDs)

### What are CRDs?
Custom Resource Definitions (CRDs) allow you to extend Kubernetes API by defining custom resources.

### When are CRDs used?
CRDs are used when you want to introduce your own object types into Kubernetes. This is useful for creating domain-specific abstractions or for integrating external services with Kubernetes.

### Example
Let's say you want to introduce a custom resource type called "Database":

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: databases.mycompany.com
spec:
  group: mycompany.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                engine:
                  type: string
                version:
                  type: string
  scope: Namespaced
  names:
    plural: databases
    singular: database
    kind: Database
    shortNames:
    - db
```

After applying this CRD, you can create Database objects:

```yaml
apiVersion: mycompany.com/v1
kind: Database
metadata:
  name: my-database
spec:
  engine: postgres
  version: "12"
```

This allows you to manage databases as native Kubernetes objects.
