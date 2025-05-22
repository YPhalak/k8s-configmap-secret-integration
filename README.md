# Kubernetes ConfigMap & Secret Integration Demo

This project demonstrates how to use **ConfigMaps** and **Secrets** in Kubernetes Deployments to securely and dynamically pass configuration values to your application.

---

## 📁 Project Structure

├── configmap.yaml # Defines key-value pairs for app config
├── secret.yaml # Defines sensitive data like passwords
├── deployment.yaml # Deployment using ConfigMap & Secret
└── README.md # Project documentation


---

## 📌 Objectives

- Create a ConfigMap and use it:
  - As environment variables
  - (Optional) As volume mounts

- Create a Secret and use it:
  - As environment variables
  - (Optional) As volume mounts

---

## ⚙️ Prerequisites

- A working Kubernetes cluster (tested with Kind/Minikube)
- `kubectl` installed and configured

## 🚀 Setup Instructions

### 1. Create the ConfigMap

Edit and apply:

# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: "production"
  APP_DEBUG: "false"

# Create configmap using below cmd
kubectl apply -f configmap.yaml

2. Create the Secret

# secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
data:
  DB_PASSWORD: eweerf46454#$%hf   # base64 encoded value of "password123"

# Create secret using below cmd
kubectl apply -f secret.yaml

3. Create the Deployment
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: config-secret-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
      - name: demo-container
        image: alpine
        command: ["/bin/sh", "-c"]
        args: ["while true; do printenv | grep APP_; sleep 30; done"]
        env:
        - name: APP_ENV
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_ENV
        - name: APP_DEBUG
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_DEBUG
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: app-secret
              key: DB_PASSWORD

Apply it:
kubectl apply -f deployment.yaml

🔍 Verification Steps
Check ConfigMap:
kubectl describe configmap app-config

Check Secret:
kubectl describe secret app-secret

Verify Environment Variables Inside Pod:
kubectl get pods
kubectl exec -it <pod-name> -- sh
printenv | grep APP_

####FINISH#########
