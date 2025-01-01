# Deploy application with k8s, ingress and cert (lets encrypts). We will use AKS (Azure Kubernetes Service) to demo. If you're using Kubernetes in AWS or GCP that would be the same.
This is reference from https://viblo.asia/p/bao-mat-nginx-ingress-voi-cert-manager-tren-kubernetes-MkNLrbYwLgA but modify a bit to use with AKS

1. Login to azure
```
az login
```
2. Login to k8s cluster
```
az aks get-credentials --resource-group <your-resource-group> --name <your-k8s-cluster>
```