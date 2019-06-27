AWS EKS
https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html

```
eksctl create cluster \
--name prod \
--version 1.13 \
--nodegroup-name standard-workers \
--node-type t3.medium \
--nodes 3 \
--nodes-min 1 \
--nodes-max 4 \
--node-ami auto
```

```
[ℹ]  using region ap-southeast-2
[ℹ]  setting availability zones to [ap-southeast-2c ap-southeast-2b ap-southeast-2a]
[ℹ]  subnets for ap-southeast-2c - public:192.168.0.0/19 private:192.168.96.0/19
[ℹ]  subnets for ap-southeast-2b - public:192.168.32.0/19 private:192.168.128.0/19
[ℹ]  subnets for ap-southeast-2a - public:192.168.64.0/19 private:192.168.160.0/19
[ℹ]  nodegroup "standard-workers" will use "ami-082dfea752d9163f6" [AmazonLinux2/1.13]
[ℹ]  creating EKS cluster "prod" in "ap-southeast-2" region
[ℹ]  will create 2 separate CloudFormation stacks for cluster itself and the initial nodegroup
[ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=ap-southeast-2 --name=prod'
[ℹ]  2 sequential tasks: { create cluster control plane "prod", create nodegroup "standard-workers" }
[ℹ]  building cluster stack "eksctl-prod-cluster"
[ℹ]  deploying stack "eksctl-prod-cluster"
[ℹ]  building nodegroup stack "eksctl-prod-nodegroup-standard-workers"
[ℹ]  deploying stack "eksctl-prod-nodegroup-standard-workers"
[✔]  all EKS cluster resource for "prod" had been created
[✔]  saved kubeconfig as "/Users/gerrysmyth/.kube/config"
[ℹ]  adding role "arn:aws:iam::792674195280:role/eksctl-prod-nodegroup-standard-wo-NodeInstanceRole-8YCBRSYS1OG" to auth ConfigMap
[ℹ]  nodegroup "standard-workers" has 0 node(s)
[ℹ]  waiting for at least 1 node(s) to become ready in "standard-workers"
[ℹ]  nodegroup "standard-workers" has 3 node(s)
[ℹ]  node "ip-192-168-19-210.ap-southeast-2.compute.internal" is ready
[ℹ]  node "ip-192-168-42-61.ap-southeast-2.compute.internal" is not ready
[ℹ]  node "ip-192-168-85-131.ap-southeast-2.compute.internal" is not ready
[ℹ]  kubectl command should work with "/Users/gerrysmyth/.kube/config", try 'kubectl get nodes'
[✔]  EKS cluster "prod" in "ap-southeast-2" region is ready
```

### Tearing down:

`kubectl get deployments`  
```
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
hello-deploy   10/10   10           10          106m
pingtest       3/3     3            3           4h6m
```  
`kubectl delete deployments`
  error: resource(s) were provided, but no name, label selector, or --all flag specified
`kubectl delete deployments --all`  
deployment.extensions "hello-deploy" deleted
deployment.extensions "pingtest" deleted

`kubectl get deployments`  
No resources found.

`kubectl get pods`  
```
NAME                            READY   STATUS        RESTARTS   AGE
hello-deploy-5f7bbff969-9c75s   1/1     Terminating   0          107m
hello-deploy-5f7bbff969-fwxw9   1/1     Terminating   0          107m
hello-deploy-5f7bbff969-h9rw6   1/1     Terminating   0          107m
hello-deploy-5f7bbff969-k9mf5   1/1     Terminating   0          107m
hello-deploy-5f7bbff969-pfx2r   1/1     Terminating   0          107m
hello-deploy-5f7bbff969-qlwn6   1/1     Terminating   0          107m
hello-deploy-5f7bbff969-rkwx5   1/1     Terminating   0          107m
hello-deploy-5f7bbff969-v7bmr   1/1     Terminating   0          107m
hello-deploy-5f7bbff969-z7k24   1/1     Terminating   0          107m
hello-deploy-5f7bbff969-zj2rk   1/1     Terminating   0          107m
pingtest-7c8b7dc6b9-28ktd       1/1     Terminating   4          4h6m
pingtest-7c8b7dc6b9-dpzwv       1/1     Terminating   4          4h6m
pingtest-7c8b7dc6b9-ghv7l       1/1     Terminating   4          4h6m
```  
`kubectl delete pods --all`  
No resources found  
`kubectl get pods`  
No resources found.  
`kubectl get cluster`  
error: the server doesn't have a resource type "cluster"
`kubectl get nodes`  
```
NAME                                                STATUS   ROLES    AGE    VERSION
ip-192-168-19-210.ap-southeast-2.compute.internal   Ready    <none>   5h6m   v1.13.7-eks-c57ff8
ip-192-168-42-61.ap-southeast-2.compute.internal    Ready    <none>   5h6m   v1.13.7-eks-c57ff8
ip-192-168-85-131.ap-southeast-2.compute.internal   Ready    <none>   5h6m   v1.13.7-eks-c57ff8
```  
`eksctl get cluster`  
```
NAME	REGION
prod	ap-southeast-2
(k8s-sample-apps) gerrysmyth@Gerrys-MBP:~/dev/code/k8s/Course_Kubernetes_Deep_Dive_NP/lesson-networking$ eksctl delete cluster
[✖]  --name must be set
(k8s-sample-apps) gerrysmyth@Gerrys-MBP:~/dev/code/k8s/Course_Kubernetes_Deep_Dive_NP/lesson-networking$ eksctl delete cluster --name prod
[ℹ]  using region ap-southeast-2
[ℹ]  deleting EKS cluster "prod"
[✔]  kubeconfig has been updated
[ℹ]  2 sequential tasks: { delete nodegroup "standard-workers", delete cluster control plane "prod" [async] }
[ℹ]  will delete stack "eksctl-prod-nodegroup-standard-workers"
[ℹ]  waiting for stack "eksctl-prod-nodegroup-standard-workers" to get deleted
[ℹ]  will delete stack "eksctl-prod-cluster"
[✔]  all cluster resources were deleted
```

### Useful commands:
`kubectl get pods`  
`kubectl get nodes`  
`kubectl get pods -o wide`  produces
```
NAME                            READY   STATUS    RESTARTS   AGE    IP               NODE                                                NOMINATED NODE   READINESS GATES
hello-deploy-5f7bbff969-9c75s   1/1     Running   0          25m    192.168.81.228   ip-192-168-85-131.ap-southeast-2.compute.internal   <none>           <none>
hello-deploy-5f7bbff969-fwxw9   1/1     Running   0          25m    192.168.88.98    ip-192-168-85-131.ap-southeast-2.compute.internal   <none>           <none>
hello-deploy-5f7bbff969-h9rw6   1/1     Running   0          25m    192.168.51.202   ip-192-168-42-61.ap-southeast-2.compute.internal    <none>           <none>
hello-deploy-5f7bbff969-k9mf5   1/1     Running   0          25m    192.168.20.33    ip-192-168-19-210.ap-southeast-2.compute.internal   <none>           <none>
hello-deploy-5f7bbff969-pfx2r   1/1     Running   0          25m    192.168.2.113    ip-192-168-19-210.ap-southeast-2.compute.internal   <none>           <none>
hello-deploy-5f7bbff969-qlwn6   1/1     Running   0          25m    192.168.20.90    ip-192-168-19-210.ap-southeast-2.compute.internal   <none>           <none>
hello-deploy-5f7bbff969-rkwx5   1/1     Running   0          25m    192.168.87.247   ip-192-168-85-131.ap-southeast-2.compute.internal   <none>           <none>
hello-deploy-5f7bbff969-v7bmr   1/1     Running   0          25m    192.168.60.236   ip-192-168-42-61.ap-southeast-2.compute.internal    <none>           <none>
hello-deploy-5f7bbff969-z7k24   1/1     Running   0          25m    192.168.76.55    ip-192-168-85-131.ap-southeast-2.compute.internal   <none>           <none>
hello-deploy-5f7bbff969-zj2rk   1/1     Running   0          25m    192.168.57.115   ip-192-168-42-61.ap-southeast-2.compute.internal    <none>           <none>
pingtest-7c8b7dc6b9-28ktd       1/1     Running   2          164m   192.168.75.134   ip-192-168-85-131.ap-southeast-2.compute.internal   <none>           <none>
pingtest-7c8b7dc6b9-dpzwv       1/1     Running   2          164m   192.168.39.24    ip-192-168-42-61.ap-southeast-2.compute.internal    <none>           <none>
pingtest-7c8b7dc6b9-ghv7l       1/1     Running   2          164m   192.168.26.203   ip-192-168-19-210.ap-southeast-2.compute.internal   <none>           <none>
```
