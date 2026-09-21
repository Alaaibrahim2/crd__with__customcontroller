# 🚀 Custom Kubernetes Controller with CRD

A practical Kubernetes project demonstrating how to extend the Kubernetes API using a **Custom Resource Definition (CRD)** and a **Custom Controller**.

The controller watches a custom resource called `CustomConfigMap` and automatically creates or deletes a corresponding Kubernetes `ConfigMap`.

## 🏗️ Architecture

```text
                 Kubernetes API Server
                         │
                         ▼
                ┌─────────────────┐
                │      CRD        │
                │ CustomConfigMap │
                └────────┬────────┘
                         │
                    Watch Events
                         │
                         ▼
                ┌─────────────────┐
                │ Custom Controller│
                │    Python        │
                └────────┬────────┘
                         │
                  Reconcile Action
                         │
                         ▼
                ┌─────────────────┐
                │    ConfigMap    │
                └─────────────────┘
```

## 🎯 What This Project Demonstrates

* Creating a Kubernetes **CRD**
* Creating and managing a **Custom Resource (CR)**
* Building a custom controller using the **Kubernetes Python Client**
* Watching `ADDED`, `MODIFIED`, and `DELETED` events
* Automatically creating and deleting ConfigMaps
* Containerizing the controller with Docker
* Deploying the controller inside Kubernetes
* Configuring **RBAC** using `ServiceAccount`, `ClusterRole`, and `ClusterRoleBinding`

## 📁 Project Components

```text
.
├── CCM_CRD.yaml
├── CCM_CR.yaml
├── custom_controller.py
├── Dockerfile
├── controller_deployment.yaml
├── clusterrole.yaml
├── clusterrolebinding.yaml
└── serviceaccount.yaml
```

## 🔄 How It Works

1. The `CustomConfigMap` CRD extends the Kubernetes API.
2. A `CustomConfigMap` resource is created.
3. The custom controller continuously watches the resource.
4. When the controller receives an event, it extracts the resource name and `spec`.
5. The controller creates or deletes the corresponding `ConfigMap`.
6. Deleting the `CustomConfigMap` results in deletion of the corresponding `ConfigMap`.

## ⚙️ Quick Start

### 1. Create the CRD

```bash
kubectl apply -f CCM_CRD.yaml
```

Verify:

```bash
kubectl get crd | grep customconfigmaps.anvesh.com
```

### 2. Create the Custom Resource

```bash
kubectl apply -f CCM_CR.yaml
```

Verify:

```bash
kubectl get customconfigmap
kubectl get ccm
```

### 3. Build the Controller

```bash
docker build -t custom-controller:v1 .
```

Push the image to Docker Hub or another container registry and update the controller deployment accordingly.

### 4. Configure RBAC

```bash
kubectl apply -f serviceaccount.yaml
kubectl apply -f clusterrole.yaml
kubectl apply -f clusterrolebinding.yaml
```

### 5. Deploy the Controller

```bash
kubectl apply -f controller_deployment.yaml
```

Check the controller:

```bash
kubectl get pods
```

## 🧪 Testing

Check that the controller and generated ConfigMap exist:

```bash
kubectl get pods
kubectl get customconfigmaps
kubectl get configmaps
```

Modify the custom resource:

```bash
kubectl edit customconfigmaps my-custom-resource-instance
```

Delete the custom resource:

```bash
kubectl delete customconfigmaps my-custom-resource-instance
```

After deletion, verify that the corresponding ConfigMap is also removed:

```bash
kubectl get configmaps
```

## 🧠 Controller Logic

The controller uses:

```python
config.load_incluster_config()
```

to load Kubernetes configuration from inside the cluster.

It communicates with the Custom Resource through:

```python
client.CustomObjectsApi()
```

and continuously watches the resource using:

```python
watch.Watch().stream(...)
```

When an event is received, the controller extracts:

```text
event['object']
event['type']
metadata.name
spec
```

and performs the appropriate ConfigMap operation.

## 📊 Result

```text
CustomConfigMap Created
        │
        ▼
Controller Detects ADDED Event
        │
        ▼
ConfigMap Created
```

```text
CustomConfigMap Deleted
        │
        ▼
Controller Detects DELETED Event
        │
        ▼
ConfigMap Deleted
```

## 🛠️ Prerequisites

* Kubernetes cluster
* `kubectl`
* Docker
* Basic Kubernetes knowledge
* Python Kubernetes client

## 💡 Key Takeaway

This project demonstrates the fundamental Kubernetes **CRD + Controller** pattern:

> **CRD extends the Kubernetes API, while the Controller watches the custom resource and takes action to move the cluster toward the desired state.**

This pattern is the foundation for building Kubernetes operators and application-specific automation.
