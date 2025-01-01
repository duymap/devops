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

5. Install ingress-nginx controller
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0-beta.0/deploy/static/provider/cloud/deploy.yaml
```
- Then check ingress-nginx controller installed: `kubectl get svc -n ingress-nginx`. The result will looks like:
```
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.0.170.192   20.28.40.188   80:31685/TCP,443:32703/TCP   75m
ingress-nginx-controller-admission   ClusterIP      10.0.127.65    <none>         443/TCP                      75m
```
- Then create subdomain for IP `20.28.40.188`. You can access Azure portal, search for Public IP, select public IP `20.28.40.188`, find out `Configuration` section, input subdomain todo.australiacentral.cloudapp.azure.com. This step we can re-use subdomain created by azure. But you can create your own subdomain by creating A record in your DNS site management.
![image](https://github.com/user-attachments/assets/4fc4f7bf-bb43-4725-a0c0-df67a5126c35)

6. Update ingress.yml by replace `<your-domain>` with `todo.australiacentral.cloudapp.azure.com`, save it
7. Apply ingress.yml:
```
kubectl apply -f ./ingress.yml
```

8. You need to install cert-manager (https://cert-manager.io/) that is a plugin of k8s to issue cert and auto re-new it every 3 months. It uses letsencrypt:
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.16.2/cert-manager.yaml
```
- Then confirm if cert-manager installed successfully by: `kubectl get pods -n cert-manager`. The result will be looks like:
```
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-57d855897b-gcdpb              1/1     Running   0          74m
cert-manager-cainjector-5c7f79b84b-dsrbs   1/1     Running   0          74m
cert-manager-webhook-657b9f664c-qwzmd      1/1     Running   0          74m
```

9. Open issuer.yml, at `email` section, update `<your-email>` by your real email. Then apply it:
```
kubectl apply -f ./issuer.yml
```
- Then verify if issuer is ready: `kubectl get issuer`. The result will be looks like:
```
NAME               READY   AGE
letsencrypt-prod   True    58s
```

10. Update ingress.yml again, add/modify as below section:
