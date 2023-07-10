Kubernetes API server -
 if kube API-Server is down then you can't use kubeconfig
 
Check node status -> kubectl get nodes
            kubectl describe node node_name
Login into node: 
   systemctl status kubelet
   systemctl start  kubelet 
   systemctl enable kubelet 

In kubeadm cluster setup several service or component of the k8 cluster run as pods 
- To check the status of these components use:
- kubectl get pods -n kube-system
- kubectl describe pod podname -n kube-system

- kubectl describe node nodename

## CHecking cluster and NOde logs 
- Service logs
   - journalctl -u kubelet/docker

- Cluster component Logs
   /var/log/kube-apiserver.log
   /var/log/kube-scheduler.log
   /var/log/kube-controller-manager.log

kubectl logs -n kube-system <pod_name>

## Troubleshoot the Apps
kubectl get pods 
kubectl describe pods pod name 
kubectl exec podname -c containername -- command {--stdin --ttyy --/bin/sh }


- Checking container logs.

- kubectl logs podname -c containeranme 



  
            
