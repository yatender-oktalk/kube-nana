# Kubernetes Learning Repository

A comprehensive Kubernetes learning repository with practical examples for deploying MongoDB and Mongo Express applications.

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Repository Structure](#repository-structure)
- [Kubernetes Concepts](#kubernetes-concepts)
- [Deployment Guide](#deployment-guide)
- [Essential kubectl Commands](#essential-kubectl-commands)
- [Troubleshooting](#troubleshooting)
- [Additional Resources](#additional-resources)

---

## Overview

This repository provides hands-on examples for learning Kubernetes fundamentals, including:

- **MongoDB Deployment**: A complete MongoDB setup with ConfigMaps, Secrets, Deployments, and Services
- **Mongo Express**: A web-based MongoDB admin interface
- **Dashboard Configuration**: Kubernetes Dashboard with Ingress
- **kubectl Cheatsheet**: A comprehensive command reference

## Prerequisites

Before you begin, ensure you have the following installed:

- [Docker](https://docs.docker.com/get-docker/) - Container runtime
- [kubectl](https://kubernetes.io/docs/tasks/tools/) - Kubernetes command-line tool
- [Minikube](https://minikube.sigs.k8s.io/docs/start/) - Local Kubernetes cluster (for local development)

### Verify Installation

```bash
# Check Docker
docker --version

# Check kubectl
kubectl version --client

# Check Minikube
minikube version
```

## Quick Start

### 1. Start Minikube Cluster

```bash
# Start minikube with default settings
minikube start

# Or with specific driver (docker recommended)
minikube start --driver=docker
```

### 2. Deploy MongoDB

```bash
# Create secret first (contains credentials)
kubectl apply -f mongo/secret.yaml

# Create ConfigMap
kubectl apply -f mongo/configmap.yaml

# Deploy MongoDB
kubectl apply -f mongo/deployment.yaml

# Create MongoDB Service
kubectl apply -f mongo/service.yaml
```

### 3. Deploy Mongo Express

```bash
# Deploy Mongo Express
kubectl apply -f mongo-express/deployment.yaml

# Create Mongo Express Service
kubectl apply -f mongo-express/service.yaml
```

### 4. Access the Application

```bash
# Get the Mongo Express URL
minikube service mongo-express-service --url

# Or use port-forward
kubectl port-forward service/mongo-express-service 8081:8081
```

Visit `http://localhost:8081` in your browser.

## Repository Structure

```
kube-nana/
├── README.md              # This documentation file
├── cheatsheet.md          # Comprehensive kubectl command reference
├── dashboard/
│   └── ingress.yaml       # Kubernetes Dashboard Ingress configuration
├── mongo/
│   ├── configmap.yaml     # MongoDB ConfigMap
│   ├── deployment.yaml    # MongoDB Deployment
│   ├── secret.yaml        # MongoDB credentials (base64 encoded)
│   └── service.yaml       # MongoDB Service
└── mongo-express/
    ├── deployment.yaml    # Mongo Express Deployment
    ├── ingress.yaml       # Mongo Express Ingress
    └── service.yaml       # Mongo Express Service
```

## Kubernetes Concepts

### Pods

A Pod is the smallest deployable unit in Kubernetes, representing a single instance of a running process.

```bash
# List all pods
kubectl get pods

# Get detailed pod information
kubectl describe pod <pod-name>

# Check pod logs
kubectl logs <pod-name>

# Execute commands in a pod
kubectl exec -it <pod-name> -- /bin/bash
```

### Deployments

Deployments manage the desired state for Pods and ReplicaSets, providing declarative updates.

```bash
# List deployments
kubectl get deployments

# Scale a deployment
kubectl scale deployment <name> --replicas=3

# Update image
kubectl set image deployment/<name> <container>=<image>:<tag>

# Check rollout status
kubectl rollout status deployment/<name>

# Rollback
kubectl rollout undo deployment/<name>
```

### Services

Services provide stable networking for Pods, enabling communication within and outside the cluster.

**Types of Services:**
- **ClusterIP** (default): Internal cluster access only
- **NodePort**: Exposes service on each node's IP at a static port
- **LoadBalancer**: Exposes service externally using a cloud provider's load balancer

```bash
# List services
kubectl get services

# Create a service
kubectl expose deployment <name> --port=<port> --type=<type>
```

### ConfigMaps

ConfigMaps store non-confidential configuration data as key-value pairs.

```bash
# Create from literal
kubectl create configmap <name> --from-literal=key=value

# Create from file
kubectl create configmap <name> --from-file=<path>

# View ConfigMap
kubectl get configmap <name> -o yaml
```

### Secrets

Secrets store sensitive data like passwords, tokens, and keys.

```bash
# Create secret
kubectl create secret generic <name> --from-literal=password=mypassword

# Encode value (for YAML)
echo -n 'mypassword' | base64

# Decode value
echo 'bXlwYXNzd29yZA==' | base64 --decode
```

### Namespaces

Namespaces provide logical isolation for resources within a cluster.

```bash
# List namespaces
kubectl get namespaces

# Create namespace
kubectl create namespace <name>

# Get resources in namespace
kubectl get all -n <namespace>
```

### Ingress

Ingress manages external access to services, typically HTTP/HTTPS routing.

```bash
# Enable ingress in minikube
minikube addons enable ingress

# Apply ingress configuration
kubectl apply -f <ingress.yaml>

# Get ingress
kubectl get ingress
```

## Deployment Guide

### Step-by-Step: MongoDB + Mongo Express

#### Step 1: Apply Secrets

Secrets must be created first as they are referenced by deployments.

```bash
kubectl apply -f mongo/secret.yaml
```

#### Step 2: Apply ConfigMaps

```bash
kubectl apply -f mongo/configmap.yaml
```

#### Step 3: Deploy MongoDB

```bash
kubectl apply -f mongo/deployment.yaml
kubectl apply -f mongo/service.yaml
```

#### Step 4: Verify MongoDB is Running

```bash
# Check pod status
kubectl get pods -l app=mongodb

# Check logs
kubectl logs -l app=mongodb

# Test connectivity
kubectl exec -it $(kubectl get pod -l app=mongodb -o jsonpath='{.items[0].metadata.name}') -- mongosh --eval "db.adminCommand('ping')"
```

#### Step 5: Deploy Mongo Express

```bash
kubectl apply -f mongo-express/deployment.yaml
kubectl apply -f mongo-express/service.yaml
```

#### Step 6: Access Mongo Express

```bash
# Get service URL (Minikube)
minikube service mongo-express-service --url
```

### Setting Up Ingress (Optional)

```bash
# Enable ingress controller
minikube addons enable ingress

# Apply ingress configuration
kubectl apply -f mongo-express/ingress.yaml

# Add to /etc/hosts (replace with minikube IP)
echo "$(minikube ip) mongo-express.com" | sudo tee -a /etc/hosts
```

## Essential kubectl Commands

For a complete reference, see [cheatsheet.md](./cheatsheet.md).

### Pod Management

```bash
# Get pods
kubectl get pods

# Describe pod
kubectl describe pod <pod-name>

# Get logs
kubectl logs <pod-name>
kubectl logs -f <pod-name>         # Follow logs
kubectl logs --tail=100 <pod-name> # Last 100 lines

# Execute command
kubectl exec -it <pod-name> -- /bin/bash

# Copy files
kubectl cp <pod-name>:/path/file ./local-file
```

### Deployment Management

```bash
# Scale up
kubectl scale deployment <name> --replicas=5

# Scale down
kubectl scale deployment <name> --replicas=1

# Update image
kubectl set image deployment/<name> <container>=<image>:<tag>

# Restart deployment
kubectl rollout restart deployment/<name>

# Rollback
kubectl rollout undo deployment/<name>
```

### Monitoring and Debugging

```bash
# Get events
kubectl get events --sort-by='.lastTimestamp'

# Resource usage
kubectl top pods
kubectl top nodes

# Describe resources
kubectl describe <resource-type> <name>
```

## Troubleshooting

### Common Issues

#### Pod in CrashLoopBackOff

```bash
# Check logs
kubectl logs <pod-name>
kubectl logs <pod-name> --previous

# Check events
kubectl describe pod <pod-name>
```

#### Pod in Pending State

```bash
# Check events for scheduling issues
kubectl describe pod <pod-name>

# Check node resources
kubectl top nodes
kubectl describe nodes
```

#### Service Not Accessible

```bash
# Verify endpoints
kubectl get endpoints <service-name>

# Check service configuration
kubectl describe service <service-name>

# Test from another pod
kubectl run debug --image=busybox -it --rm -- wget -O- <service-name>:<port>
```

#### Image Pull Errors

```bash
# Check pod events
kubectl describe pod <pod-name>

# Verify image name and tag
kubectl get deployment <name> -o jsonpath='{.spec.template.spec.containers[*].image}'
```

### Useful Debug Commands

```bash
# Run a debug pod
kubectl run debug --image=busybox -it --rm -- /bin/sh

# Check DNS resolution
kubectl run debug --image=busybox -it --rm -- nslookup <service-name>

# Check network connectivity
kubectl run debug --image=busybox -it --rm -- wget -qO- <service-name>:<port>

# Check cluster components
kubectl get componentstatuses

# View all resources
kubectl get all --all-namespaces
```

## Additional Resources

### Official Documentation

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [kubectl Reference](https://kubernetes.io/docs/reference/kubectl/)
- [Kubernetes Tutorials](https://kubernetes.io/docs/tutorials/)

### Tools

- [Minikube](https://minikube.sigs.k8s.io/) - Local Kubernetes cluster
- [k9s](https://k9scli.io/) - Terminal-based Kubernetes UI
- [Lens](https://k8slens.dev/) - Kubernetes IDE
- [Helm](https://helm.sh/) - Kubernetes package manager

### Learning Resources

- [Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [Play with Kubernetes](https://labs.play-with-k8s.com/)
- [Killercoda Kubernetes](https://killercoda.com/kubernetes) - Interactive scenarios

---

## License

This repository is for educational purposes.

## Contributing

Feel free to submit issues and pull requests to improve this repository.
