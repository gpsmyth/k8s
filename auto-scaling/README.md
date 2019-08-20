## Prior notes
### Obtaining metrics for AWS
https://github.com/kubernetes-incubator/metrics-server/

git clone git@github.com:kubernetes-incubator/metrics-server.git

## More Notes
Partly using https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/

From
https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html


## Starting off...  Horizontal Pod Autoscalar
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

## Cluster-autoscaler
Used [eks cluster autoscaler setup](https://aws.amazon.com/premiumsupport/knowledge-center/eks-cluster-autoscaler-setup/)

* As per usual - started with
```
eksctl create cluster --name myeks --version 1.13 --nodegroup-name standard-workers --node-type t3.medium --nodes 1 --nodes-min 1 --nodes-max 10 --node-ami auto --asg-access
```

The `--asg-access` is useful in that IAM access is setup, along with the necessary ASG tags  
```python
Key: k8s.io/cluster-autoscaler/enabled
Key: k8s.io/cluster-autoscaler/<ClusterName> .e.g. as per above <ClusterName> would be myeks
```

* Deploy the Cluster Autoscaler  
`wget https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml`

File added as some properties have changed
> Only property to change was \<My Cluster Name\>

* Create the autoscaler deployment
`kubectl apply -f cluster-autoscaler-autodiscover.yaml`
```
serviceaccount/cluster-autoscaler created
clusterrole.rbac.authorization.k8s.io/cluster-autoscaler created
role.rbac.authorization.k8s.io/cluster-autoscaler created
clusterrolebinding.rbac.authorization.k8s.io/cluster-autoscaler created
rolebinding.rbac.authorization.k8s.io/cluster-autoscaler created
deployment.apps/cluster-autoscaler created
```

* Check the deployment logs for errors
`kubectl logs -f deployment/cluster-autoscaler -n kube-system`

* Set uo and test scaling  
** Ideally better to have performed **declaratively**  
`kubectl create deployment autoscaler-demo --image=nginx`  
`kubectl scale deployment autoscaler-demo --replicas=50`  
```
kubectl get deploy
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
autoscaler-demo   12/50   50           12          3m52s
```
** pod count increasing

```
kubectl get nodes
NAME                                                STATUS   ROLES    AGE   VERSION
ip-192-168-27-223.ap-southeast-2.compute.internal   Ready    <none>   13m   v1.13.8-eks-cd3eb0
ip-192-168-32-193.ap-southeast-2.compute.internal   Ready    <none>   45s   v1.13.8-eks-cd3eb0
ip-192-168-49-50.ap-southeast-2.compute.internal    Ready    <none>   35s   v1.13.8-eks-cd3eb0
ip-192-168-89-131.ap-southeast-2.compute.internal   Ready    <none>   42s   v1.13.8-eks-cd3eb0
```
** nodes are increasing

```
kubectl get rs
NAME                         DESIRED   CURRENT   READY   AGE
autoscaler-demo-7d467dd49b   50        50        20      5m45s
```

```
kubectl get deploy
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
autoscaler-demo   50/50   50           50          7m2s
```
`kubectl describe pods autoscaler-demo > autoscaler-demo_pods_describe.txt`


Then back down to 10 replicas
`kubectl get deploy`
```
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
autoscaler-demo   10/10   10           10          29m
```
`kubectl describe pods autoscaler-demo > autoscaler-demo_pods_describe_scale_down.txt`

Cleaning up
```
kubectl delete deployment autoscaler-demo
eksctl delete cluster --name myeks
```