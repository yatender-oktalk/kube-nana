# kubectl Cheatsheet

## Cluster Info
```bash
kubectl cluster-info
kubectl get nodes
```

## Pod Operations
```bash
kubectl get pods
kubectl get pods -o wide
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/bash
```

## Deployment Operations
```bash
kubectl get deployments
kubectl create deployment <name> --image=<image>
kubectl scale deployment <name> --replicas=<number>
kubectl rollout status deployment/<name>
kubectl rollout undo deployment/<name>
```

## Service Operations
```bash
kubectl get services
kubectl expose deployment <name> --port=<port> --type=LoadBalancer
```

## Namespace Operations
```bash
kubectl get namespaces
kubectl create namespace <name>
kubectl get pods --namespace=<name>
```

## ConfigMap and Secret
```bash
kubectl get configmaps
kubectl create configmap <name> --from-file=<path>
kubectl get secrets
kubectl create secret generic <name> --from-literal=key=value
```

## Apply/Delete Resources
```bash
kubectl apply -f <filename.yaml>
kubectl delete -f <filename.yaml>
```

## Resource Usage
```bash
kubectl top nodes
kubectl top pods
```

## Context and Configuration
```bash
kubectl config view
kubectl config current-context
kubectl config use-context <context-name>
```

## Port Forwarding
```bash
kubectl port-forward <pod-name> <local-port>:<pod-port>
```

## Minikube Specific
```bash
minikube service <service-name> --url
minikube dashboard
minikube addons list
minikube start --driver=<driver-name>
minikube stop
minikube start
```

## Install Ingress controller

```bash
minikube service <service-name> --url
minikube dashboard
minikube addons list
minikube start --driver=<driver-name>
minikube addons disable dashboard
minikube addons enable dashboard
```

## Get 
```bash
kubectl get all -n kubernetes-dashboard

```


```bash
k apply -f dashboard/ingress.yaml
```