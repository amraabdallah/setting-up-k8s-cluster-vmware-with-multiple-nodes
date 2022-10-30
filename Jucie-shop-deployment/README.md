# How to install nginx ingress controller

this demo will show you how to install nginx ingress using Helm

## Steps
- Install Helm v3 [Docs](https://helm.sh/docs/intro/install/)
```
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

- Install nginx controller [Docs](https://kubernetes.github.io/ingress-nginx/deploy/)
```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```
It will install the controller in the ingress-nginx namespace, creating that namespace if it doesn't already exist.
>if the ingress controller is not installed, it will install it,
>if the ingress controller is already installed, it will upgrade it.

- check if the controller is deployed successfully
```
kubectl get pods --namespace=ingress-nginx
```
Now you can start using ingress in your cluster
