WHat is a service/account - In k8s a service a/c is an accound used by container processes within
pods to authenticate with the k8s API.

if your Pods need 2 communicate with d K8s-API U can use service accounts to control their access

apiVersion: v1
kind: ServiceAccount
metadata: 
   name: my-serviceaccount

We can bind service a/c with a role-binding or cluster binding. 
kubectl create sa <name_f_service> -n default

then create a role bindinign to bind the service account to the role. 
