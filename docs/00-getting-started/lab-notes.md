# 🧪 Lab Notes – Getting Started

Welcome! This is your first notebook entry in the GROW program.  
Use this space to document the setup of your **Kubernetes learning environment** and reflect on what you’re configuring.

---

## ✅ Goals
By the end of this section, you will:
- Install and configure basic Kubernetes CLI tools
- Create your first local Kubernetes cluster using `kind`
- Deploy and inspect your first Pod
- Verify your setup is working for the labs ahead

---

## 🛠️ Step-by-Step Setup Instructions

### Step 1: Install `kubectl`
Follow the official guide:  
➡️ https://kubernetes.io/docs/tasks/tools/install-kubectl/

Verify:
```bash
kubectl version --client
```

### Step 2: Install `kind` (Kubernetes IN Docker)
Follow the official guide:  
➡️ https://kind.sigs.k8s.io/docs/user/quick-start/

Verify:
```bash
kind version
```

---

### Step 3: Create a Local Cluster
```bash
kind create cluster --name grow-lab
```

Check your nodes:
```bash
kubectl get nodes
```

---

### Step 4: Deploy Your First Pod
```bash
kubectl run nginx --image=nginx
kubectl get pods
```

Describe it:
```bash
kubectl describe pod nginx
```

Delete it:
```bash
kubectl delete pod nginx
```

---

## 🎯 Next Step

<!-- Original template (Kubernetes Fundamentals is not published for now):
When ready, move on to your first official lab:  
👉 [Kubernetes Fundamentals – Deploying a Pod and Deployment](../01-kubernetes-fundamentals/lab-guide.md)
-->

When ready, move on to the first topic of the CKA study group:  
👉 [1. Introducing the Certified Kubernetes Administrator](../cka/01-introducing-cka/index.md)
