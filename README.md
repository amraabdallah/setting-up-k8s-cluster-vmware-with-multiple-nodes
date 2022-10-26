# Set up a Kubernetes Cluster using Kubeadm utility on Ubuntu 20.04 VMs

Hello! in this example I'll explain how to set up a k8s cluster using 2 VMs (1 control-plane and 1 worker-node) sure you can set up as many nodes as you want!.
Just follow the steps:
This has been tested on Ubuntu 20.04 vm (I'm using WMware Workstation).

## Pre-requisites

 1. **Two** or more virtual machines  running on **Ubuntu 20.04**.
 2. One VM will act as the control plane or master node and the rest will be worker nodes. 

## Instructions (***run on each machine***)

- Assuming you just spun your vms so run the following to
  update the cache and update the packages
```
sudo apt update && sudo apt upgrade -y 
```

- install some utilites that we will need
```
sudo apt install curl vim ssh 
```

- appened the host IP and name to the /etc/hosts file
```
echo $(hostname -i) $(hostname) >> /etc/hosts
```

- disable the swap memory by commenting it in the /etc/fstab file
```
sudo sed -i "/swap/s/^/#/" /etc/fstab
```
```
sudo swapoff -a
```

- authenticate and add the packages
``` 
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
```
```
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
```
    
- add the version of k8s we want to use as an enviroment variable I used 1.20.7-00
```
KUBE_DPKG_VERSION=1.20.7-00
```

- now install the prerequisite packages
```
apt-get update
apt-get install -y ebtables ethtool
apt-get install -y docker.io
apt-get install -y apt-transport-https
apt-get install -y kubelet=$KUBE_DPKG_VERSION kubeadm=$KUBE_DPKG_VERSION kubectl=$KUBE_DPKG_VERSION
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash
```
    
- This is a requirement for Container Network Interface (CNI) plug-ins to work.
```
. /etc/os-release
sysctl net.bridge.bridge-nf-call-iptables=1
```

## Configure the control plane (***run on the control plane only***)

- export the k8s version to be installed
```
KUBE_VERSION=1.20.7
```
- define the pod-network-cidr block
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --kubernetes-version=$KUBE_VERSION
```
- create a directory for the cluster config file
```
mkdir -p $HOME/.kube
```
- copy the config file to the directory we just created
```
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```

- chaning the ownership of the config to current user
```
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

- then apply the following configurations
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl apply -f rbac.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```
- After successful initialization of the Kubernetes master **go to each worker node** and apply the kubeadm command you get to let them join the cluster it should be something similar to this
```
kubeadm join (CP-IP):6443 --token ywb1yo.p1iuogfvqsov1jjcw     --discovery-token-ca-cert-hash sha256:fe944ssd5ecvf52e06ce602ad233a963b2e7da79bdsa5cs3dd5d95cv41be 
```
- Now, Everything should work fine, and you should be able to execute the current command and they should be in the ready state, Hopefully :)
```
kubectl get nodes -o wide
```