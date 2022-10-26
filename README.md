# Set up a Kubernetes Cluster using Kubeadm utility on Ubuntu 20.04 VMs

Hello! in this example I'll explain how to set up a k8s cluster using 2 VMs (1 control-plane and 1 worker-node) sure you can set up as many nodes as you want!.
Just follow the steps:
This has been tested on Ubuntu 20.04 vm (I'm using WMware Workstation).

## Pre-requisites

 1. **Two** or more virtual machines  running on **Ubuntu 20.04**.
 2. One VM will act as the control plane or master node and the rest will be worker nodes. 

## Instructions (The following on all of your vms)
 Assuming you just spun your vms so run the following 
 update the cache and update the packages
` sudo apt update && sudo apt upgrade -y `
install some utilites that we will need
` sudo apt install curl vim ssh `
appened the host IP and name to the /etc/hosts file
`echo $(hostname -i) $(hostname) >> /etc/hosts`
disable the swap memory by commenting it in the /etc/fstab file
`sudo sed -i "/swap/s/^/#/" /etc/fstab`
`sudo swapoff -a`
authenticate and add the packages
   ` curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -`

    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
    EOF
add the version of k8s we want to use as an enviroment variable I used 1.20.7-00
`KUBE_DPKG_VERSION=1.20.7-00`

now install the prerequisite packages

    apt-get update
    apt-get install -y ebtables ethtool
    apt-get install -y docker.io
    apt-get install -y apt-transport-https
    apt-get install -y kubelet=$KUBE_DPKG_VERSION kubeadm=$KUBE_DPKG_VERSION kubectl=$KUBE_DPKG_VERSION
    curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash
This is a requirement for Container Network Interface (CNI) plug-ins to work.

    . /etc/os-release
    sysctl net.bridge.bridge-nf-call-iptables=1
