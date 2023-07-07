Q1 - Installing Kubeadm and Kubelet packages on the control-plane and the workernode-1 
     Using the version of 1.26.0-00 for both 

- Set forwarding rules first on both control plane and node-1 

```
 cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

Next confirm the distro before
- cat /etc/*-release #to confirm the distro


sudo swapoff -a   #maybe needed 
#### Update the apt package index and install packages needed to use the Kubernetes apt repository:
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl  

#### Download the Google Cloud public signing key: But make a directory first 

mkdir /etc/apt/keyrings/
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg

#### Add the Kubernetes apt repository:
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

#### Update apt package index, install kubelet, kubeadm and kubectl, and pin their version: 
- Follow the instruction here

sudo apt  update
sudo apt  install -y kubelet=1.26.0-00 kubeadm=1.26.0-00 kubectl=1.26.0-00    #if version asked to install is 1.26 version 
sudo apt-mark hold kubelet kubeadm kubectl

## Q2 What version of the kublet is installed. 
- Kublet --verson

## Q3 How manay noded are part o the kubernetes cluster currently? 
- kubectl get nodes  # Answer is 0 if we have not intialized our cluster.

-  Hence to initialize our cluseter and set the apiserver-advertise-address to ip on the eth0 control node, api-server-cert-extra-sasns set
   to control node and the pod-network-cidr to 10.244.0.0/16
   once done also set the default kubeconfig file and wait for the node to be part of the cluster.

-   kubeadm init --apiserver-advertise-address=192.168.0.14 --pod-network-cidr=10.244.0.0/16
{--kuberbetes-version 1.24.0}

After initializing the cluster the 
We have to configure/setup the default kubeconfig file.
by running the result of the result in the init command.  # So that we can intereac wit the cluster using kubectl. run on the control plane

## Q4 Ask node 1 to to join the cluster using a join token. 
 kubeadm token create --print-join-command
 sudo kubeadm join 192.168.34.2 --token 34546srs.34sdfsd \
     --discovery-token-ca-cert-hash  sha256:43fserreeffefffsfehruhewjfgbaksfakuhkehfewhorwefhhohawhe48302
kubectl get nodes 
# Q5 Install a network pluggin on a documentation 

- Network and Netowrk policy

- Install flannel
kubectl apply -f https:flannel
kubectl get nodes 
  # kubectl get pods -A
     






