### Setting a default storage class

[Useful Note](https://netapp-trident.readthedocs.io/en/stable-v18.07/kubernetes/operations/tasks/storage-classes.html)   
```  
kubectl patch storageclass <storage-class-name> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'  
```

```
kubectl patch storageclass slow -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
```

You should only have one default storage class in your cluster at any given time. Kubernetes does not technically prevent you from having more than one, but it will behave as if there is no default storage class at all.

### From above, the following was performed  

`kubectl describe sc slow`  
```
Name:            slow  
IsDefaultClass:  Yes  
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{"storageclass.kubernetes.io/is-default-class":"true"},"name":"slow"},"mountOptions":["debug"],"parameters":{"type":"gp2","zones":"ap-southeast-2a"},"provisioner":"kubernetes.io/aws-ebs","reclaimPolicy":"Retain"}
,storageclass.kubernetes.io/is-default-class=true
Provisioner:           kubernetes.io/aws-ebs
Parameters:            type=gp2,zones=ap-southeast-2a
AllowVolumeExpansion:  <unset>
MountOptions:
  debug
ReclaimPolicy:      Retain
VolumeBindingMode:  Immediate
Events:             <none>
```
`kubectl describe sc gp2`  
```
Name:            gp2
IsDefaultClass:  Yes
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{"storageclass.kubernetes.io/is-default-class":"true"},"name":"gp2"},"parameters":{"fsType":"ext4","type":"gp2"},"provisioner":"kubernetes.io/aws-ebs"}
,storageclass.kubernetes.io/is-default-class=true
Provisioner:           kubernetes.io/aws-ebs
Parameters:            fsType=ext4,type=gp2
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```  
`kubectl get sc`  
```
NAME             PROVISIONER             AGE
fast             kubernetes.io/aws-ebs   29m
gp2 (default)    kubernetes.io/aws-ebs   39m
slow (default)   kubernetes.io/aws-ebs   29m
```  
`kubectl patch storageclass slow -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'`   
storageclass.storage.k8s.io/slow patched  
`kubectl get sc`  
NAME            PROVISIONER             AGE
fast            kubernetes.io/aws-ebs   62m
gp2 (default)   kubernetes.io/aws-ebs   71m
slow            kubernetes.io/aws-ebs   62m


`eksctl delete cluster --name prod`
