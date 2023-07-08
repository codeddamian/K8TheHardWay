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
