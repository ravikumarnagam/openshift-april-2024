# Day 2

## Lab - Deploying an application into openshift using declarative style(using yaml files)

Kubernetes/Openshift supports deploying and managing application in two style
1. Imperative style ( using commands )
2. Using source code/manifest files(yaml files)

Deleting existing deployments
```
oc project jegan
oc get all
oc delete deploy/nginx svc/nginx
```

Let's auto-generate yaml file to deploy nginx into openshift declaratively
```
oc create deploy nginx --image=bitnami/nginx --replicas=3 --dry-run=client -o yaml
oc create deploy nginx --image=bitnami/nginx --replicas=3 --dry-run=client -o yaml > nginx-deploy.yml
oc apply -f nginx-deploy.yml
oc get deploy,rs,po
```

## Lab - Scale up nginx deployment pod counts to 5 from 3
```
oc get po
```

Edit the nginx-deploy.yml file and update the replicas from 3 to 5 and save it.

Now you may apply as shown below to scale up the nginx deployment to create a total of 5 pods
```
oc apply -f nginx-deploy.yml
oc get po
```

## Lab - Deleting a deployment in declarative style
```
oc get all
oc delete -f nginx-deploy.yml
oc get all
```

Expected output
<pre>
[root@tektutor.org declarative-manifest-scripts]# ls
nginx-deploy.yml
[root@tektutor.org declarative-manifest-scripts]# oc get all
Warning: apps.openshift.io/v1 DeploymentConfig is deprecated in v4.14+, unavailable in v4.10000+
NAME                        READY   STATUS    RESTARTS   AGE
pod/nginx-94c4bd68b-4bqzn   1/1     Running   0          11m
pod/nginx-94c4bd68b-89j2v   1/1     Running   0          5m23s
pod/nginx-94c4bd68b-m8sw7   1/1     Running   0          5m23s
pod/nginx-94c4bd68b-phpjq   1/1     Running   0          11m
pod/nginx-94c4bd68b-x79bn   1/1     Running   0          11m

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   5/5     5            5           11m

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-94c4bd68b   5         5         5       11m

[root@tektutor.org declarative-manifest-scripts]# oc delete -f nginx-deploy.yml 
deployment.apps "nginx" deleted
  
[root@tektutor.org declarative-manifest-scripts]# oc get all
Warning: apps.openshift.io/v1 DeploymentConfig is deprecated in v4.14+, unavailable in v4.10000+
No resources found in jegan namespace.  
</pre>
