# kubernetes-install-in-ubuntu-20-kubeadm-install-in-ubuntu-20-Kubernetes-cluster-on-Ubuntu-20.04-
kubernetes install in ubuntu 20 =kubeadm install in ubuntu 20 =Kubernetes cluster on Ubuntu 20.04 , 
=========================---------------------------------===================================


Login as root user

sudo su -
--------------------
Perform all the commands as root user unless otherwise specified
Disable Firewall

ufw disable

Disable swap

swapoff -a; sed -i '/swap/d' /etc/fstab
--------------------------------------
Update sysctl settings for Kubernetes networking

cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
----------------------------------------
Install docker engine

{
  apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  apt update
  apt install -y docker-ce=5:19.03.10~3-0~ubuntu-focal containerd.io
}
---------------------------------------
Kubernetes Setup
Add Apt repository

{
  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
}
-----------------------------------------------
Install Kubernetes components

apt update && apt install -y kubeadm kubelet kubectl
-----------------------------------------------------------
In case you are using LXC containers for Kubernetes nodes

Hack required to provision K8s v1.15+ in LXC containers

{
  mknod /dev/kmsg c 1 11
  echo '#!/bin/sh -e' >> /etc/rc.local
  echo 'mknod /dev/kmsg c 1 11' >> /etc/rc.local
  chmod +x /etc/rc.local
}
----------------------------------------------------------
On kmaster
Initialize Kubernetes Cluster

Update the below command with the ip address of kmaster

kubeadm init --apiserver-advertise-address=172.16.16.100 --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all
OR OR 
sudo kubeadm init --pod-network-cidr=10.244.0.0/16


  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

  export KUBECONFIG=/etc/kubernetes/admin.conf


kubeadm join 192.168.122.103:6443 --token etlbem.un3nsyo072l91yot \
	--discovery-token-ca-cert-hash sha256:0d213c6eef7dc1d7f83c9eb82e2ccd8b24182d0caf436e8d04a046450e823800 

-----------------------------------------------------
Deploy WEAVE network
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml   
-------------------------------------------
kubectl get nodes
hai@i7laptop:~$ kubectl get nodes 
NAME     STATUS   ROLES           AGE   VERSION
master   Ready    control-plane   15d   v1.25.4
==============================================   bellow steps in worker nodes===================
sudo systemctl stop firewalld 
sudo rm  -rf /etc/containerd/config.toml
sudo  systemctl restart containerd
hai@i7laptop:~$ sudo kubeadm join 192.168.122.103:6443 --token etlbem.un3nsyo072l91yot \
	--discovery-token-ca-cert-hash sha256:0d213c6eef7dc1d7f83c9eb82e2ccd8b24182d0caf436e8d04a046450e823800 
================================================================= completed ========
hai@i7laptop:~$ kubectl get nodes 
NAME     STATUS   ROLES           AGE   VERSION
master   Ready    control-plane   15d   v1.25.4
worker   Ready    <none>          15d   v1.25.4
===============================================================  if any  errors  ====
//////////////////////////////////////////////////////////////////////////////////////////
hai@master:~$ sudo kubeadm init --pod-network-cidr=10.244.0.0/16
I1218 06:45:37.145708  633010 version.go:256] remote version is much newer: v1.26.0; falling back to: stable-1.25
[init] Using Kubernetes version: v1.25.5
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR Port-6443]: Port 6443 is in use
	[ERROR Port-10259]: Port 10259 is in use
	[ERROR Port-10257]: Port 10257 is in use
	[ERROR FileAvailable--etc-kubernetes-manifests-kube-apiserver.yaml]: /etc/kubernetes/manifests/kube-apiserver.yaml already exists
	[ERROR FileAvailable--etc-kubernetes-manifests-kube-controller-manager.yaml]: /etc/kubernetes/manifests/kube-controller-manager.yaml already exists
	[ERROR FileAvailable--etc-kubernetes-manifests-kube-scheduler.yaml]: /etc/kubernetes/manifests/kube-scheduler.yaml already exists
	[ERROR FileAvailable--etc-kubernetes-manifests-etcd.yaml]: /etc/kubernetes/manifests/etcd.yaml already exists
	[ERROR Port-2379]: Port 2379 is in use
	[ERROR Port-2380]: Port 2380 is in use
	[ERROR DirAvailable--var-lib-etcd]: /var/lib/etcd is not empty
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher

SOLUTIONS:= 
systemctl stop firewalld 

hai@master:~$ sudo kubeadm reset 

hai@master:~$ sudo rm -rf /etc/kubernetes/manifests/kube-apiserver.yaml 

hai@master:~$ sudo rm -rf /etc/kubernetes/manifests/kube-controller-manager.yaml 

hai@master:~$ sudo rm  -rf  /etc/kubernetes/manifests/kube-scheduler.yaml

hai@master:~$ sudo rm -rf /etc/kubernetes/manifests/etcd.yaml
 hai@master:~$ sudo rm  -rf /etc/containerd/config.toml
hai@master:~$ sudo  systemctl restart containerd

================= THANK YOU ==========================

