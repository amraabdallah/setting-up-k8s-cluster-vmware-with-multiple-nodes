# How to install nginx ingress controller

this demo will show you how to install nginx ingress using Helm and use it on a bare metal cluster to deploy an application 

## Steps
- Install MetalLB v0.13.7 [Installation Docs](https://metallb.universe.tf/installation/) which is a load balancer solution for bare metal clusters.
```
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.7/config/manifests/metallb-native.yaml
```
Then apply the following configuration:
```
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: cheap
  namespace: metallb-system
spec:
  addresses:
  - 192.168.254.100-192.168.254.110
```

>These addresses will be used by metallb (load balancers end points) <br />
Change addresses range based on your network configs to get that run the following commands <br /> ```ip a s ``` <br />
install the sipcalc utility and run: ```sipcalc ip/cidr ``` 
in my case the command was:``` sipcalc 192.168.254.156/24 ```<br />
then you can get a range for the LBs as the example above then apply. <br />
Now the loadbalancer solution is ready you can test it with a load balancer service.
___



- Install Helm v3 [Docs](https://helm.sh/docs/intro/install/)
```
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

- add the  nginx ingress controller repo [Docs](https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-helm/)
```
helm repo add nginx-stable https://helm.nginx.com/stable
helm repo update
```
- Create a file with following values which will be passed to helm
```
controller:
  config:
   use-forwarded-headers: "true"
   server-tokens: "False"
  service:
   loadBalancerIP: 192.168.254.100
```
>Again don't forget to change the loadBalancerIP field to your range.

- Install the nginx ingress controller (you can create a namespace for it if you want) and pass the values file
```
helm install [name is as you want] nginx-stable/nginx-ingress -f internal.yaml
```
-Now check the services in your cluster you should see nginx-ingress service with the type LoadBalancer with assigned external IP
```
kubectl get svc
```

Now you can start using ingress in your cluster to deploy applications.
