# kubectl Cheatsheet

A comprehensive guide to essential Kubernetes commands for managing pods, deployments, and cluster resources.

## Table of Contents

- [Cluster Info](#cluster-info)
- [Pod Operations](#pod-operations)
- [Checking Logs](#checking-logs)
- [Executing Commands in Pods](#executing-commands-in-pods)
- [Deployment Operations](#deployment-operations)
- [Deploying New Images](#deploying-new-images)
- [Scaling (Upscale/Downscale)](#scaling-upscaledownscale)
- [Service Operations](#service-operations)
- [Namespace Operations](#namespace-operations)
- [ConfigMap and Secret](#configmap-and-secret)
- [Apply/Delete Resources](#applydelete-resources)
- [Resource Usage](#resource-usage)
- [Context and Configuration](#context-and-configuration)
- [Port Forwarding](#port-forwarding)
- [Troubleshooting](#troubleshooting)
- [Minikube Specific](#minikube-specific)
- [Install Ingress Controller](#install-ingress-controller)
- [Kubernetes Dashboard](#kubernetes-dashboard)

---

## Cluster Info

```bash
# Get cluster information
kubectl cluster-info

# List all nodes in the cluster
kubectl get nodes

# Get detailed information about a specific node
kubectl describe node <node-name>

# Get node resource usage
kubectl top nodes
```

## Pod Operations

```bash
# List all pods in current namespace
kubectl get pods

# List pods with more details (IP, Node)
kubectl get pods -o wide

# List all pods in all namespaces
kubectl get pods --all-namespaces

# Get detailed information about a specific pod
kubectl describe pod <pod-name>

# Watch pods in real-time
kubectl get pods -w

# Get pods with specific labels
kubectl get pods -l app=<label-value>

# Delete a pod
kubectl delete pod <pod-name>

# Force delete a pod (useful for stuck pods)
kubectl delete pod <pod-name> --grace-period=0 --force
```

## Checking Logs

```bash
# Get logs from a pod
kubectl logs <pod-name>

# Get logs from a specific container in a pod
kubectl logs <pod-name> -c <container-name>

# Follow/stream logs in real-time
kubectl logs -f <pod-name>

# Get logs from previous container instance (after crash)
kubectl logs <pod-name> --previous

# Get last N lines of logs
kubectl logs <pod-name> --tail=100

# Get logs with timestamps
kubectl logs <pod-name> --timestamps

# Get logs from all pods with a specific label
kubectl logs -l app=<label-value>

# Get logs since a specific time
kubectl logs <pod-name> --since=1h

# Get logs since a specific timestamp
kubectl logs <pod-name> --since-time=2024-01-01T00:00:00Z
```

## Executing Commands in Pods

```bash
# Execute an interactive bash shell in a pod
kubectl exec -it <pod-name> -- /bin/bash

# Execute an interactive shell (if bash is not available)
kubectl exec -it <pod-name> -- /bin/sh

# Execute a specific command in a pod
kubectl exec <pod-name> -- <command>

# Execute command in a specific container
kubectl exec -it <pod-name> -c <container-name> -- /bin/bash

# List files in pod
kubectl exec <pod-name> -- ls -la /app

# Check environment variables in pod
kubectl exec <pod-name> -- env

# Check running processes
kubectl exec <pod-name> -- ps aux

# Copy files from local to pod
kubectl cp <local-file-path> <pod-name>:<pod-file-path>

# Copy files from pod to local
kubectl cp <pod-name>:<pod-file-path> <local-file-path>
```

## Deployment Operations

```bash
# List all deployments
kubectl get deployments

# Get deployments with more details
kubectl get deployments -o wide

# Get detailed information about a deployment
kubectl describe deployment <deployment-name>

# Create a deployment
kubectl create deployment <name> --image=<image>

# Check rollout status
kubectl rollout status deployment/<name>

# View rollout history
kubectl rollout history deployment/<name>

# Undo to previous deployment
kubectl rollout undo deployment/<name>

# Undo to a specific revision
kubectl rollout undo deployment/<name> --to-revision=<revision-number>

# Pause a rollout
kubectl rollout pause deployment/<name>

# Resume a paused rollout
kubectl rollout resume deployment/<name>

# Restart a deployment (rolling restart)
kubectl rollout restart deployment/<name>
```

## Deploying New Images

```bash
# Update deployment with a new image
kubectl set image deployment/<deployment-name> <container-name>=<new-image>:<tag>

# Example: Update nginx container to version 1.19
kubectl set image deployment/nginx-deployment nginx=nginx:1.19

# Update image using edit (opens in editor)
kubectl edit deployment <deployment-name>

# Update image using patch
kubectl patch deployment <deployment-name> -p '{"spec":{"template":{"spec":{"containers":[{"name":"<container-name>","image":"<new-image>:<tag>"}]}}}}'

# Update using apply with modified YAML
kubectl apply -f <updated-deployment.yaml>

# View container images in a deployment
kubectl get deployment <deployment-name> -o jsonpath='{.spec.template.spec.containers[*].image}'
```

## Scaling (Upscale/Downscale)

```bash
# Scale deployment to specific number of replicas (upscale)
kubectl scale deployment <deployment-name> --replicas=5

# Scale down a deployment
kubectl scale deployment <deployment-name> --replicas=1

# Scale to zero (stop all pods)
kubectl scale deployment <deployment-name> --replicas=0

# Scale multiple deployments at once
kubectl scale deployment <deployment1> <deployment2> --replicas=3

# Autoscale based on CPU usage
kubectl autoscale deployment <deployment-name> --min=2 --max=10 --cpu-percent=80

# Get horizontal pod autoscaler status
kubectl get hpa

# Describe horizontal pod autoscaler
kubectl describe hpa <hpa-name>

# Delete horizontal pod autoscaler
kubectl delete hpa <hpa-name>

# Scale a ReplicaSet
kubectl scale replicaset <replicaset-name> --replicas=3

# Scale a StatefulSet
kubectl scale statefulset <statefulset-name> --replicas=3
```

## Service Operations

```bash
# List all services
kubectl get services

# Get service details
kubectl describe service <service-name>

# Expose deployment as a service
kubectl expose deployment <name> --port=<port> --type=LoadBalancer

# Create ClusterIP service
kubectl expose deployment <name> --port=<port> --type=ClusterIP

# Create NodePort service
kubectl expose deployment <name> --port=<port> --type=NodePort

# Get service endpoints
kubectl get endpoints <service-name>

# Delete a service
kubectl delete service <service-name>
```

## Namespace Operations

```bash
# List all namespaces
kubectl get namespaces

# Create a new namespace
kubectl create namespace <name>

# Get pods in a specific namespace
kubectl get pods --namespace=<name>

# Get all resources in a namespace
kubectl get all -n <namespace>

# Set default namespace for kubectl commands
kubectl config set-context --current --namespace=<namespace>

# Delete a namespace (and all resources in it)
kubectl delete namespace <name>
```

## ConfigMap and Secret

```bash
# List all ConfigMaps
kubectl get configmaps

# Create ConfigMap from file
kubectl create configmap <name> --from-file=<path>

# Create ConfigMap from literal values
kubectl create configmap <name> --from-literal=key1=value1 --from-literal=key2=value2

# Get ConfigMap details
kubectl describe configmap <name>

# View ConfigMap data
kubectl get configmap <name> -o yaml

# List all secrets
kubectl get secrets

# Create secret from literal values
kubectl create secret generic <name> --from-literal=key=value

# Create secret from file
kubectl create secret generic <name> --from-file=<path>

# View secret data (base64 encoded)
kubectl get secret <name> -o yaml

# Decode secret value
kubectl get secret <name> -o jsonpath='{.data.<key>}' | base64 --decode
```

## Apply/Delete Resources

```bash
# Apply configuration from file
kubectl apply -f <filename.yaml>

# Apply all files in a directory
kubectl apply -f <directory>/

# Apply configuration from URL
kubectl apply -f <url>

# Delete resources from file
kubectl delete -f <filename.yaml>

# Delete all resources in a directory
kubectl delete -f <directory>/

# Delete all pods in a namespace
kubectl delete pods --all -n <namespace>

# Dry run to see what would be applied
kubectl apply -f <filename.yaml> --dry-run=client

# Validate YAML without applying
kubectl apply -f <filename.yaml> --validate=true --dry-run=server
```

## Resource Usage

```bash
# Get resource usage for nodes
kubectl top nodes

# Get resource usage for pods
kubectl top pods

# Get resource usage for pods in all namespaces
kubectl top pods --all-namespaces

# Get resource usage for containers in a pod
kubectl top pod <pod-name> --containers

# Sort pods by CPU usage
kubectl top pods --sort-by=cpu

# Sort pods by memory usage
kubectl top pods --sort-by=memory
```

## Context and Configuration

```bash
# View kubectl configuration
kubectl config view

# Get current context
kubectl config current-context

# List all contexts
kubectl config get-contexts

# Switch to a different context
kubectl config use-context <context-name>

# Set a cluster in kubeconfig
kubectl config set-cluster <cluster-name> --server=<server-url>

# Set credentials for a user
kubectl config set-credentials <user-name> --token=<token>

# Delete a context
kubectl config delete-context <context-name>
```

## Port Forwarding

```bash
# Forward local port to pod port
kubectl port-forward <pod-name> <local-port>:<pod-port>

# Forward to a service
kubectl port-forward service/<service-name> <local-port>:<service-port>

# Forward to deployment
kubectl port-forward deployment/<deployment-name> <local-port>:<container-port>

# Forward on all interfaces (not just localhost)
kubectl port-forward --address 0.0.0.0 <pod-name> <local-port>:<pod-port>
```

## Troubleshooting

```bash
# Get events for troubleshooting
kubectl get events

# Get events sorted by time
kubectl get events --sort-by='.lastTimestamp'

# Get events for a specific namespace
kubectl get events -n <namespace>

# Debug a pod by getting detailed info
kubectl describe pod <pod-name>

# Check why a pod is pending
kubectl get pod <pod-name> -o yaml | grep -A 10 status

# Get pod status and conditions
kubectl get pod <pod-name> -o jsonpath='{.status.conditions}'

# Run a debug container
kubectl run debug --image=busybox -it --rm -- /bin/sh

# Attach to a running container
kubectl attach <pod-name> -it

# Create a debug pod in same namespace
kubectl debug <pod-name> -it --image=busybox

# Check cluster component status
kubectl get componentstatuses

# Check API resources available
kubectl api-resources

# Explain a resource type
kubectl explain pod.spec.containers

# Get raw API response
kubectl get --raw /api/v1/namespaces
```

## Minikube Specific

```bash
# Start minikube cluster
minikube start

# Start with specific driver
minikube start --driver=<driver-name>

# Stop minikube cluster
minikube stop

# Delete minikube cluster
minikube delete

# Get service URL
minikube service <service-name> --url

# Open service in browser
minikube service <service-name>

# Open Kubernetes dashboard
minikube dashboard

# List all addons
minikube addons list

# Enable an addon
minikube addons enable <addon-name>

# Disable an addon
minikube addons disable <addon-name>

# SSH into minikube node
minikube ssh

# Get minikube IP
minikube ip

# Check minikube status
minikube status
```

## Install Ingress Controller

```bash
# Enable ingress addon in minikube
minikube addons enable ingress

# Verify ingress controller is running
kubectl get pods -n ingress-nginx

# Get ingress resources
kubectl get ingress

# Describe ingress
kubectl describe ingress <ingress-name>

# Apply ingress configuration
kubectl apply -f <ingress.yaml>

# Apply dashboard ingress
kubectl apply -f dashboard/ingress.yaml
```

## Kubernetes Dashboard

```bash
# Enable dashboard addon
minikube addons enable dashboard

# Disable dashboard addon
minikube addons disable dashboard

# Get all resources in kubernetes-dashboard namespace
kubectl get all -n kubernetes-dashboard

# Open dashboard in browser
minikube dashboard
```

## Quick Reference

### Common Patterns

```bash
# Get all resources
kubectl get all

# Get all resources in all namespaces
kubectl get all --all-namespaces

# Output in YAML format
kubectl get <resource> <name> -o yaml

# Output in JSON format
kubectl get <resource> <name> -o json

# Watch resources
kubectl get <resource> -w

# Filter by label
kubectl get <resource> -l key=value

# Sort output
kubectl get <resource> --sort-by=<field>
```

### Useful Aliases

Add these to your `.bashrc` or `.zshrc`:

```bash
alias k='kubectl'
alias kgp='kubectl get pods'
alias kgd='kubectl get deployments'
alias kgs='kubectl get services'
alias kgn='kubectl get nodes'
alias kd='kubectl describe'
alias kl='kubectl logs'
alias ke='kubectl exec -it'
alias ka='kubectl apply -f'
alias kdel='kubectl delete -f'
```