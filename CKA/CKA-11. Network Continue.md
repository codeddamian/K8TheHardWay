Ip Addr Managemnt. 


Service Networking. 
k8 services provide. away to expose an application running as a set of pods. 
It is just and abstract way for clients to access application without needing to be aware of the application pods. 

-> So a  client makes request to a service which routes the traffic to its Pods in a load-balanced fashion. 
 - To know which pod a service is routing traaffic to you can look at the endpoint

  - use a service for pods to access and another pod. - services is hosted accross the cluster.
  - - service used to access application only within the cluster is called -> cluster IP
    - a service used to access application from outside the cluster is called -> NodePort

 - Cluster Ip -> Expose app inside the cluster network used by client inside the cluster
 - NodePort -> Expose app outside the cluster network. so client is outside the cluster
 - LoadBalancer -> only used during cloud platforms
 - ExrernalNAme.

   Example:  3- node cluster no pods or services yet
   - each kublet on each node whatcch the changes oin the cluster throough thte api-server. and create the pods on the nodes and then invoke the cni plugin to configure network for the pod the ip and networking 
   - 
   - kube-proxy  watches the changes in. the cluster through API-server  and when there's a new service is to be created and it gets into action.
        - When a service onject is created in k8  it is assigned an ip addr from a pre-defined range and the
  -  Kube-proxy components runinig on each node get that ip address and creates forwarding rules on each node in the cluster saying that any traffic coming to this ip the ip of the serverice should go the ip of the POD. once that us unplac, whenever a pOD tries to reach the iP of the service, it is forwarrded to the pods ip address which is accessible from an any node in the cluster. Now remeber it is not just the IP it's an IP and port combination.
  -
  -   Whenever services are creat or deleted the  kube-proxy components creates or deletes the rules. so how are these rules creatd? kube-proxy supports different ways such as userspace where kube-proxy listens on. aport for each service and proxies connections to the pods. By creating ipvs rules or the third and defalut option and the one familiar to us is using iptables. The proxy mode can be set using the proc=xymode option while configuring the kube-proxy service. if this isnot set it default ti iptables. so we'll see how iptabes are configured by k-proxy. \
  -
  -
  - . 
   
