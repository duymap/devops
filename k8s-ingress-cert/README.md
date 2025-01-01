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

3. Create secret (for environment of docker to deploy in k8s)
```
kubectl apply -f ./secret.yml
```

4. Deploy apps:
```
kubectl apply -f ./deployment.yml 
```
- Then verify pods: ` kubectl get pods`
```
NAME                       READY   STATUS    RESTARTS      AGE
backend-6997b6d44-lbqmn    1/1     Running   1 (76m ago)   77m
db-6d99dfccdc-rzgsv        1/1     Running   0             77m
frontend-68f56f8cf-ggwcn   1/1     Running   0             77m
```

- Then veriry service: `kubectl get svc`. The result will looks like:
```
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)        AGE
backend      ClusterIP      10.0.96.33     <none>         80/TCP         79m
db           ClusterIP      10.0.15.148    <none>         80/TCP         79m
frontend     LoadBalancer   10.0.211.214   20.28.47.135   80:30398/TCP   79m
kubernetes   ClusterIP      10.0.0.1       <none>         443/TCP        106m
```
Then we can verify by access external IP 20.28.47.135:
![image](https://github.com/user-attachments/assets/d1f507a7-c2fd-435b-afd0-9b21581f8015)
