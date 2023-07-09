##### Back Up the etcd Data
/home/cloud_user/etcd_backup.db

- Look up the value for the key cluster.name in the etcd cluster =beebox
ETCDCTL_API=3 etcdctl get cluster.name \
  --endpoints=https://10.0.1.101:2379 \
  --cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
  --cert=/home/cloud_user/etcd-certs/etcd-server.crt \
  --key=/home/cloud_user/etcd-certs/etcd-server.key

- Back up etcd using etcdctl and the provided etcd certificates:
ETCDCTL_API=3 etcdctl snapshot save /home/cloud_user/etcd_backup.db \
  --endpoints=https://10.0.1.101:2379 \
  --cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
  --cert=/home/cloud_user/etcd-certs/etcd-server.crt \
  --key=/home/cloud_user/etcd-certs/etcd-server.key

  Reset etcd by removing all existing etcd data:
     - sudo systemctl stop etcd
     - sudo rm -rf /var/lib/etcd
 
  #### Restore the etcd Data from the Backup

Restore the etcd data from the backup (this command spins up a temporary etcd cluster, saving
the data from the backup file to a new data directory in the same location where the previous 
data directory was):

sudo ETCDCTL_API=3 etcdctl snapshot restore /home/cloud_user/etcd_backup.db \
  --initial-cluster etcd-restore=https://10.0.1.101:2380 \
  --initial-advertise-peer-urls https://10.0.1.101:2380 \
  --name etcd-restore \
  --data-dir /var/lib/etcd

Set ownership on the new data directory: 
      -  sudo chown -R etcd:etcd /var/lib/etcd

Start etcd: 
   -  sudo systemctl start etcd

Verify the restored data is present by looking up the value for the key cluster.name again:

ETCDCTL_API=3 etcdctl get cluster.name \   #Beebox
  --endpoints=https://10.0.1.101:2379 \
  --cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
  --cert=/home/cloud_user/etcd-certs/etcd-server.crt \
  --key=/home/cloud_user/etcd-certs/etcd-server.key   



## WORKING WITH ETCDCTL

 

etcdctl is a command line client for etcd.

**Look at the ETCD Logs OR check the image used by ETCD pod.**
 - kubectl logs etcd-controlplane -n kube-system
 - kubectl describe pod etcd-controlplane -n kube-system

In all our Kubernetes Hands-on labs, the ETCD key-value database is deployed as a static pod on the 
master. The version used is v3.

To make use of etcdctl for tasks such as back up and restore, make sure that you set the
  - ETCDCTL_API to 3.

You can do this by exporting the variable ETCDCTL_API prior to using the etcdctl client. This can be done as follows:

export ETCDCTL_API=3
On the Master Node:

To see all the options for a specific sub-command, make use of the -h or –help flag.

 For example, if you want to take a snapshot of etcd, use:

etcdctl snapshot save -h and keep a note of the mandatory global options.

Since our ETCD database is TLS-Enabled, the following options are mandatory:

– cacert                verify certificates of TLS-enabled secure servers using this CA bundle

– cert                    identify secure client using this TLS certificate file

– endpoints=[127.0.0.1:2379] This is the default as ETCD is running on master node and exposed on localhost 2379.

– key                  identify secure client using this TLS key file

 For a detailed explanation on how to make use of the etcdctl command line tool and work with the -h flags, check out the solution video for the Backup and Restore Lab on KodeKloud
  
### To restore a /etcd cluster from back -up 

- First Restore the snapshot:

root@controlplane:~# ETCDCTL_API=3 etcdctl  --data-dir /var/lib/etcd-from-backup \
snapshot restore /opt/snapshot-pre-boot.db


Note: In this case, we are restoring the snapshot to a different directory but in the same server where we took the backup (the controlplane node) As a result, the only required option for the restore command is the --data-dir.



Next, update the /etc/kubernetes/manifests/etcd.yaml:

We have now restored the etcd snapshot to a new path on the controlplane - /var/lib/etcd-from-backup, so, the only change to be made in the YAML file, is to change the hostPath for the volume called etcd-data from old directory (/var/lib/etcd) to the new directory (/var/lib/etcd-from-backup).

  volumes:
  - hostPath:
      path: /var/lib/etcd-from-backup
      type: DirectoryOrCreate
    name: etcd-data
With this change, /var/lib/etcd on the container points to /var/lib/etcd-from-backup on the controlplane (which is what we want).

When this file is updated, the ETCD pod is automatically re-created as this is a static pod placed under the /etc/kubernetes/manifests directory.



Note 1: As the ETCD pod has changed it will automatically restart, and also kube-controller-manager and kube-scheduler. Wait 1-2 to mins for this pods to restart. You can run the command: watch "crictl ps | grep etcd" to see when the ETCD pod is restarted.

Note 2: If the etcd pod is not getting Ready 1/1, then restart it by kubectl delete pod -n kube-system etcd-controlplane and wait 1 minute.

Note 3: This is the simplest way to make sure that ETCD uses the restored data after the ETCD pod is recreated. You don't have to change anything else.



If you do change --data-dir to /var/lib/etcd-from-backup in the ETCD YAML file, make sure that the volumeMounts for etcd-data is updated as well, with the mountPath pointing to /var/lib/etcd-from-backup (THIS COMPLETE STEP IS OPTIONAL AND NEED NOT BE DONE FOR COMPLETING THE RESTORE)
  
