## K8 Management & Maintainance

##### Intro Higher Availability in K8s
Design cluster to be higher available with multiple instance of control node 1& 2 {kube-api-server] ->> load balncer ->> worker node{kublet}
- Stacketed /Etcd {design pattern}1
  - Run on the same node as the rest o f the control plane component {used by clusters setup by kubeadm}
- External /Etcd {design pattern 2}
  - Runs on seperate nodes, multiple /etcd node diff from the node on the control plane.
 ##### K8 Management Tools 
 - Kubectl
 - Minikube
 - Kubeadm
 - Helm -> template and package mananegmnet soln
 - Kompose -> to translate docker compose files into a set of K8 obtjects
 - Kustomize -> conf manangement tools for management conf objects

##### Safely draininig a K8s Node. 
What is draining - when we performing mainaintance you may sometime need to remove a K8 node from the service. 
To do this you want to drain the node. 
Hence container runnig on the node will be gracefully terminated and potentially rescheduled on an other node. 

- kubectl drain <node name>  # remove for mainatanice
- kubectl drain <node name> --ignore-daemonsets {if there are pods attached to the nodes }
- kubectl uncordon <node name>  #Read to run pods and containers and be used in the cluster


- Effects of draining a node that has a signle pod 
   1.) It can not delete pods that are not managed by a replication contriller, Job,DaemonSet, StatefulSet.
   2.)  It can not delete pods that are not managed Damon-set-managed- pods
Solution --> kubectl drain <node name> --ignore-daemonsets  --force {Will delete  all the pods on d_node but those on replica will return}
 Hence if you execute kubectl get nodes = STATUS=Ready, SchedulingDisabled -> No pod will be scheduled since out for maintainance.

#### Upgrading K8 with Kubeadm
To keep our cluster up to date we may have to upgrade k8 with kubeadm

### Controller Node Upgrade steps 
 - drain the control plane node { kubectl drain <node_name>}
 - upgrade kubeadm package
   sudo apt-get update && \
   Sudo apt-get install -y --allow-change-held-packages kubeadm=1.22.2-00
         - kubeadm version               #to confirm the version
   
 - plan the upgrade
      {sudo kubeadm upgrade plan}
 - apply the upgrade
     {kubeadm upgrade apply v1.22.2}
 - upgrade kubelet and kubectl packages
      sudo apt-get update && \
      Sudo apt-get install -y --allow-change-held-packages kubelet=1.22.2-00 kubctl=1.22.2-00

###### Just incase there's a change to the conf file we would restart the daemon and restart kubelet
        - sudo systemctl daemon-reload
        - sudo systemctl restart kubelet

- uncordon the control plane node #
     {kubectl uncordon <node name>}

### Worker Node Upgrade steps 
- Drain the node upgrade
   { kubectl drain <node_name> --ignore-daemonsets --force}
- 
- upgrade kubeadm
   - sudo apt-get update && \
   Sudo apt-get install -y --allow-change-held-packages kubeadm=1.22.2-00
   kubeadm version               #to confirm the version
- 
- upgrade the kubelet conf 
     {sudo Kubeadm upgrade node}
- upgrade kubelet and kubectl on worker plane node
     sudo apt-get update && \
     Sudo apt-get install -y --allow-change-held-packages kubelet=1.22.2-00 kubctl=1.22.2-00
##### Just incase there's a change to the conf file we would restart the daemon and restart kubelet
         - sudo systemctl daemon-reload
         - sudo systemctl restart kubelet

 - uncordon the worker plane node
     { kubectl uncordon <node_name>}


## KodeKLoud 
- Os Upgrades
   If you have multiple replicas of a pod if your node goes off > 5mins then the pods are available.
However, if the nodes without replica come back before 5 minutes then the pods come back online.
However, if the node comes back later then the pods are gone and called dead -The time it waits for a pod to comeback is called **--pod-eviction-timout=5m0s**

SO if you not certain about if a node will be back then you can drain the node. 
- kubectl drain node-1   #Hence no pods can be scheduled on it and the pods move to other nodes.
- Kubectl uncordon node-1 #  Will remove the drain on the node-1 so that pods can be scheduled on it
- Kubectl cordon node-1  #Makes a node not schedulable but doesn't drain the pod off it.

Cluster Upgrade Introduction. 
- None of the control plane components should be at a k=version abobe the kube-api-sever,
  since this the component that all the rest of the components talk to i.e if the kubeap-server is 
 at version X then all other components should be at X-1 or X-2. Not the case of the Kubectl 
 cause Kubectl can be on X+1 or X-1.

- The recommended upgrade option is from  v1.10 to v1.11 to v1.12 to v1.13


### Worker Node Upgrade steps 
- Drain the node upgrade
   { kubectl drain <node_name> --ignore-daemonsets --force}
- 
- upgrade kubeadm & Kubelet
   - apt-get upgrade -y kubeadm=1.12.0-00
   - apt-get upgrade -y kubelet=1.12.0-00
- upgrade the kubelet conf 
   sudo Kubeadm upgrade node config --kubelet-version v1.12.0
- upgrade kubelet and kubectl on worker plane node
     sudo apt-get update && \
     Sudo apt-get install -y --allow-change-held-packages kubelet=1.22.2-00 kubctl=1.22.2-00
##### Just incase there's a change to the conf file we would restart the daemon and restart kubelet
         - sudo systemctl daemon-reload
         - sudo systemctl restart kubelet
         -kubectl uncordon node-1


To know which nodes can host a workload you need tocheck the taints on them with the command
kubectl get nodes # To see the nodes that belong to the cluster then
kubectl describe nodes  controlplane | grep -i taint # to check the taints on the node

To see how many apps are hosted on a cluster check the deployments 
  - kubectl get deployments

To see how which nodes the pods are on 
 - kubectl get pods -o wide

To know the latest version on the k8 look at the version on tghe command 
- kubeadm upgrade plan  #check the remote version.

  To backup all service by querying the kube-API server 
  - kubectl get all  --all-namespaces -o yaml > file_name
  


  
   
