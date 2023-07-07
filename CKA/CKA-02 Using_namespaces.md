## Namespaces

To see all namespaces in your cluster 
- kubectl get namespace

Initial Namespace include 
- default
- kubenode-lease
  kube-public
  kube-system

Kubectl get pods --namespace my-namespace 
kubectl create namespace damain-namespace #create ns 

kubectl get pods --namespace kube-system
kubectl get pods --all-namespaces #not sure which namespace a pod is


