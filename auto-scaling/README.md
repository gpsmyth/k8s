## Prior notes
### Obtaining metrics for AWS
https://github.com/kubernetes-incubator/metrics-server/

git clone git@github.com:kubernetes-incubator/metrics-server.git

## More Notes
Partly using https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/

From
https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html


## Starting off... 
``` 
eksctl create cluster \
--name hpa-cluster \
--version 1.13 \
--nodegroup-name standard-workers \
--node-type t3.medium \
--nodes 1 \
--nodes-min 1 \
--nodes-max 4 \
--node-ami auto
```

Then to install metrcis from the root of the Metrics git clone level  
`kubectl apply -f deploy/1.8+/`

https://aws.amazon.com/premiumsupport/knowledge-center/eks-metrics-server-pod-autoscaler/ is a possible direction

kubectl apply = kubectl create + kubectl replace as per https://kubernetes.io/docs/user-guide/kubectl-overview/

kubectl apply is declarative. kubectl create + kubectl replace is imperative

`source /usr/local/etc/bash_completion.d/kubectl` 

In hpademo.yml it's good I have a NamSpace (Kind) object. Not recommended for isolating business units / customers (for that you need clusters).

hpademo.yml also has a svc object (Kind : Service) in acg-ns namespace & loadbalancer type and loadbalances pods with label of acg-stress.

So we have a namespace, svc, deployment def in a single pod.

The hpa hits the pod to create a scaling action.

`kubectl apply -f ./hpademo.yml`  
* namespace/acg-ns created
* service/acg-lb created
* deployment.apps/acg-web created
* horizontalpodautoscaler.autoscaling/acg-hpa created


`kubectl get deploy --namespace acg-ns`
```
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
acg-web   1/1     1            1           45s
```

`kubectl get hpa --namespace acg-ns`
```
NAME      REFERENCE            TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
acg-hpa   Deployment/acg-web   0%/50%    1         10        1          2m27s
```

`kubectl get nodes`  
```
NAME                                                STATUS   ROLES    AGE     VERSION
ip-192-168-65-223.ap-southeast-2.compute.internal   Ready    <none>   9m32s   v1.13.7-eks-c57ff8
```

In a separate terminal  
```
kubectl run --image=busybox -i --tty loader /bin/sh
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
If you don't see a command prompt, try pressing enter.
while true; do wget -q -O- http://acg-lb.acg-ns.svc.cluster.local;done
```

```
kubectl get hpa --namespace acg-ns
NAME      REFERENCE            TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
acg-hpa   Deployment/acg-web   495%/50%   1         10        1          21m

kubectl get deploy --namespace acg-ns
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
acg-web   8/10    10           8           22m


kubectl get hpa --namespace acg-ns
NAME      REFERENCE            TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
acg-hpa   Deployment/acg-web   63%/50%   1         10        10         23m

kubectl get deploy --namespace acg-ns
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
acg-web   8/10    10           8           22m

 kubectl get hpa --namespace acg-ns
NAME      REFERENCE            TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
acg-hpa   Deployment/acg-web   65%/50%   1         10        10         33m

kubectl get deploy --namespace=acg-ns -o yaml > gps
```

Crtl C the while loop session...  
Session ended, resume using 'kubectl attach loader-597d75fcb6-l6nm9 -c loader -i -t' command when the pod is running

```
kubectl get horizontalpodautoscalers.autoscaling --namespace=acg-ns
NAME      REFERENCE            TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
acg-hpa   Deployment/acg-web   0%/50%    1         10        1          84m
```

https://kubernetes.io/docs/tasks/tools/install-kubectl/
was used for bash-completion2



