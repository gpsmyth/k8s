## Using deployments with minikube
minikube start  
There is a newer version of minikube available (v1.2.0).  Download it here:
https://github.com/kubernetes/minikube/releases/tag/v1.2.0

```
To disable this notification, run the following:
minikube config set WantUpdateNotification false
😄  minikube v1.1.1 on darwin (amd64)
🔥  Creating virtualbox VM (CPUs=2, Memory=2048MB, Disk=20000MB) ...
🐳  Configuring environment for Kubernetes v1.14.3 on Docker 18.09.6
🚜  Pulling images ...
🚀  Launching Kubernetes ... 
⌛  Verifying: apiserver proxy etcd scheduler controller dns
🏄  Done! kubectl is now configured to use "minikube"
```
Deloyment file is originally deploy-orig.yml

```
kubectl get nodes
NAME       STATUS   ROLES    AGE   VERSION
minikube   Ready    master   96s   v1.14.3

kubectl get pods
No resources found.

kubectl apply -f ./deploy.yml 
deployment.apps/test created

kubectl get deploy test --watch
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
test   2/3     3            1           20s
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
test   3/3     3            1           21s
test   3/3     3            3           26s

kubectl get deploy test 
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
test   3/3     3            3           38s

kubectl describe deploy test
Name:                   test
Namespace:              default
CreationTimestamp:      Mon, 29 Jul 2019 12:54:33 +1200
Labels:                 run=test
Annotations:            deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"run":"test"},"name":"test","namespace":"default"},"spe...
Selector:               run=test
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        5
RollingUpdateStrategy:  0 max unavailable, 1 max surge
Pod Template:
  Labels:  run=test
  Containers:
   nginx:
    Image:        nginx:1.12
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   test-7b89f46496 (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  77s   deployment-controller  Scaled up replica set test-7b89f46496 to 3
```

## Replicas in deploy.yml now changed to 6 for scaling and with
```
kubectl apply -f ./deploy.yml we have

kubectl describe deploy test
Name:                   test
Namespace:              default
CreationTimestamp:      Mon, 29 Jul 2019 12:54:33 +1200
Labels:                 run=test
Annotations:            deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"run":"test"},"name":"test","namespace":"default"},"spe...
Selector:               run=test
Replicas:               6 desired | 6 updated | 6 total | 6 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        5
RollingUpdateStrategy:  0 max unavailable, 1 max surge
Pod Template:
  Labels:  run=test
  Containers:
   nginx:
    Image:        nginx:1.12
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   test-7b89f46496 (6/6 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  15m   deployment-controller  Scaled up replica set test-7b89f46496 to 3
  Normal  ScalingReplicaSet  10s   deployment-controller  Scaled up replica set test-7b89f46496 to 6
gerrysmyth@Gerrys-MBP:~/dev/code/k8s-acg/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get deploy test
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
test   6/6     6            6           16m

kubectl get rs
NAME              DESIRED   CURRENT   READY   AGE
test-7b89f46496   6         6         6       17m
```

## Change nginx to version 1.13
```
kubectl apply -f ./deploy.yml 
deployment.apps/test configured

kubectl rollout status deployment test
Waiting for deployment "test" rollout to finish: 1 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 1 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 1 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 2 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 2 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 2 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 2 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 3 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 3 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 3 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 3 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 4 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 4 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 4 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 4 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 5 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 5 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 5 out of 6 new replicas have been updated...
Waiting for deployment "test" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "test" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "test" rollout to finish: 1 old replicas are pending termination...
deployment "test" successfully rolled out

kubectl get rs -o wide
NAME              DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES       SELECTOR
test-7b89f46496   0         0         0       22m   nginx        nginx:1.12   pod-template-hash=7b89f46496,run=test
test-8557c84dfc   6         6         6       65s   nginx        nginx:1.13   pod-template-hash=8557c84dfc,run=test
```
shows 6 pods at nginx:1.13

## Updating App to nginx:1.14 and rollingUpdate parameter is set to 50%
### Adding record flag to kubectl apply puts the command in the automation in the object.
```
kubectl apply -f ./deploy.yml --record 
deployment.apps/test configured

kubectl get rs -o wide
NAME              DESIRED   CURRENT   READY   AGE    CONTAINERS   IMAGES       SELECTOR
test-7b69785db    6         6         6       4m3s   nginx        nginx:1.14   pod-template-hash=7b69785db,run=test
test-7b89f46496   0         0         0       32m    nginx        nginx:1.12   pod-template-hash=7b89f46496,run=test
test-8557c84dfc   0         0         0       11m    nginx        nginx:1.13   pod-template-hash=8557c84dfc,run=test

kubectl rollout status deploy test
deployment "test" successfully rolled out

kubectl rollout history deploy test
deployment.extensions/test 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         kubectl apply --filename=./deploy.yml --record=true


kubectl rollout history deployment test --revision=1
deployment.extensions/test with revision #1
Pod Template:
  Labels:	pod-template-hash=7b89f46496
	run=test
  Containers:
   nginx:
    Image:	nginx:1.12
    Port:	<none>
    Host Port:	<none>
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>

  kubectl rollout history deployment test --revision=3
deployment.extensions/test with revision #3
Pod Template:
  Labels:	pod-template-hash=7b69785db
	run=test
  Annotations:	kubernetes.io/change-cause: kubectl apply --filename=./deploy.yml --record=true
  Containers:
   nginx:
    Image:	nginx:1.14
    Port:	<none>
    Host Port:	<none>
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>
``` 
you can also do rollback commands like:  
`kubectl rollout undo deploy test`

<span style="color:red">However, <strong>this is bad as now declaritely you have nginix:14 and present state is nginix:1.13; so it's best to keep with deploy.yml.</strong>  
The rollback is an imperative operation, so out of sync with yml file.</span>
