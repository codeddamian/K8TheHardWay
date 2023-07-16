Files in a container are ephemeral - (not dependable)
   - To make the container have persistent storage. 
   - Volumes allow external storage.
{  POD ( Container )} -> PersistentVolumeClaim -> PersistentVolume -> ExternalStorage.
 - Both Volume and Persistent Volume each have volume type.
```
apiVersion: v1
kind: Pod
metadata:
  name: test-ebs
spec:
  containers:
  - image: registry.k8s.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-ebs
      name: test-volume
  volumes:                                                  volume:
  - name: test-volume                                       - name: 
    # This AWS EBS volume must already exist.                 hostPath:   
    awsElasticBlockStore:                                        path: /data
      volumeID: "<volume id>"
      fsType: ext4
```
```
apiVersion: v1
kind: Pod
metadata:
  name: configmap-pod
spec:
  containers:
    - name: test
      image: busybox:1.28
      volumeMounts:
        - name: config-vol
          mountPath: /etc/config
  volumes:
    - name: config-vol
      configMap:
        name: log-config
        items:
          - key: log_level
            path: log_level
  ```

Two types of volume 
- emptyDir is not persistent.
- hostPath persistent

- Exploring K8 Persistent Volumes.
    Persistent V
     storage classes
    persisstent vol claims

```
  kind: PersistentVolume
  apiVersion: v1
  metadata:
    nmae:myp-v
  spec:
    storageClassName: 
    persistentVolumeReclaimPolicy: Retain/Delete;Recycle
    capacity:
      storage:
    accessModes:
      - ReadWriteOnce
    hostPath:
       path: /etc/output   
 ```
```
PersistentVolumeClaim reps a useros request for storage resources. it defines a set of sttributes
similar to those of a persisten volume.
  when PVC is create it will look for a pv that is able to meet the requested criteria. if it finds one,
it will automatically be  bound to the PersistentVolume.
- Expandinig pvc by editing the storage value. --allowVolumeExpansion set to true. in the storage class

- create a storage class
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
 name local-disk
provsioner: l=k8.io/no-provsiiner
allowVolumeExpansion: true


apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
   - name: x
     image: y
     volumeMounts:
      - mountPath: "/var/www/hmtl"
        name: name
