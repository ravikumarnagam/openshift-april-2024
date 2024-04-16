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
oc expose deploy/nginx --type=ClusterIP --port=8080 --dry-run=client -o yaml > nginx-clusterip-svc.yml
oc expose deploy/nginx --type=ClusterIP --port=8080 --dry-run=client -o yaml > nginx-nodeport-svc.yml
oc expose deploy/nginx --type=ClusterIP --port=8080 --dry-run=client -o yaml > nginx-lb-svc.yml
ls -l
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

## Lab - Auto-generating service manifests file to create them declaratively later
```
ls
oc apply -f nginx-deploy.yml
oc get all
oc expose deploy/nginx --type=ClusterIP --port=8080 --dry-run=client -o yaml
oc get svc
```

Expected output
<pre>
[root@tektutor.org declarative-manifest-scripts]# ls
nginx-deploy.yml
  
[root@tektutor.org declarative-manifest-scripts]# oc apply -f nginx-deploy.yml 
deployment.apps/nginx created
  
[root@tektutor.org declarative-manifest-scripts]# oc get all
Warning: apps.openshift.io/v1 DeploymentConfig is deprecated in v4.14+, unavailable in v4.10000+
NAME                        READY   STATUS              RESTARTS   AGE
pod/nginx-94c4bd68b-96ttl   1/1     Running             0          3s
pod/nginx-94c4bd68b-m6zl6   0/1     ContainerCreating   0          3s
pod/nginx-94c4bd68b-pnvpx   1/1     Running             0          3s
pod/nginx-94c4bd68b-rbpfm   1/1     Running             0          3s
pod/nginx-94c4bd68b-rtrm8   1/1     Running             0          3s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   4/5     5            4           3s

NAME                              DESIRED   CURRENT   READY   AGE
[root@tektutor.org declarative-manifest-scripts]# oc expose deploy/nginx --type=ClusterIP --port=8080 --dry-run=client -o yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: nginx
  type: ClusterIP
status:
  loadBalancer: {}
  
[root@tektutor.org declarative-manifest-scripts]# oc expose deploy/nginx --type=ClusterIP --port=8080 --dry-run=client -o yaml > nginx-clusterip-svc.yml
  
[root@tektutor.org declarative-manifest-scripts]# ls
nginx-clusterip-svc.yml  nginx-deploy.yml
  
[root@tektutor.org declarative-manifest-scripts]# oc expose deploy/nginx --type=NodePort --port=8080 --dry-run=client -o yaml > nginx-nodeport-svc.yml
  
[root@tektutor.org declarative-manifest-scripts]# oc expose deploy/nginx --type=LoadBalancer --port=8080 --dry-run=client -o yaml > nginx-lb-svc.yml
  
[root@tektutor.org declarative-manifest-scripts]# ls -l
total 16
-rw-r--r-- 1 root root 245 Apr 16 14:26 nginx-clusterip-svc.yml
-rw-r--r-- 1 root root 293 Apr 16 14:19 nginx-deploy.yml
-rw-r--r-- 1 root root 248 Apr 16 14:27 nginx-lb-svc.yml
-rw-r--r-- 1 root root 244 Apr 16 14:26 nginx-nodeport-svc.yml
</pre>

## Lab - Create a clusterip internal service declaratively
```
oc get deploy,rs,po
oc get svc
oc apply -f nginx-clusterip-svc.yml
oc get svc
```

Expected output
<pre>
  
</pre>

## Lab - Create a nodeport external service declaratively
```
oc get deploy,rs,po
oc get svc
oc apply -f nginx-nodeport-svc.yml
oc get svc
```

Expected output
<pre>
  
</pre>

## Lab - Create a loadbalancer external service declaratively
```
oc get deploy,rs,po
oc get svc
oc apply -f nginx-lb-svc.yml
oc get svc
```

Expected output
<pre>
  
</pre>


## Lab - Creating a route to expose your application to access them outside the cluster
In Openshift, to expose a service within the cluster ClusterIP service is created and for external use route is created. 

```
oc get all
oc expose deploy/nginx --port=8080
oc get svc
oc expose svc/nginx
oc get routes
curl http://nginx-jegan.apps.ocp4.tektutor.org.labs
```

Expected output
```
[root@tektutor.org declarative-manifest-scripts]# oc get all
Warning: apps.openshift.io/v1 DeploymentConfig is deprecated in v4.14+, unavailable in v4.10000+
NAME                        READY   STATUS    RESTARTS   AGE
pod/nginx-94c4bd68b-7gcfp   1/1     Running   0          30m
pod/nginx-94c4bd68b-lsqqg   1/1     Running   0          30m
pod/nginx-94c4bd68b-r55ns   1/1     Running   0          30m

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   3/3     3            3           30m

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-94c4bd68b   3         3         3       30m

[root@tektutor.org declarative-manifest-scripts]# oc expose deploy/nginx --port=8080
service/nginx exposed
[root@tektutor.org declarative-manifest-scripts]# oc get svc
NAME    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
nginx   ClusterIP   172.30.134.110   <none>        8080/TCP   4s

[root@tektutor.org declarative-manifest-scripts]# oc expose svc/nginx
route.route.openshift.io/nginx exposed

[root@tektutor.org declarative-manifest-scripts]# oc get route
NAME    HOST/PORT                                 PATH   SERVICES   PORT   TERMINATION   WILDCARD
nginx   nginx-jegan.apps.ocp4.tektutor.org.labs          nginx      8080                 None

[root@tektutor.org declarative-manifest-scripts]# curl http://nginx-jegan.apps.ocp4.tektutor.org.labs
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

## Ingress
<pre>
- is a routing/forwarding rule
- routing forwarding rule we can using a kubernetes/openshift resource called Ingress
- there is controller which manages Ingress resources called Ingress Controller
- Ingress Controller which is part of Openshift/Kubernetes, constantly monitors for new Ingress resources created anywhere in the openshift cluster
- Whenever the Ingress Controller get to know about a new Ingress resource, it fetches ingress routing rules and then it configures the Load balancer with those routing rules

- For a Ingress to work, we need 3 types of resources
  1. Ingress ( Routing rule )
  2. Ingress Controller
     - This can be Nginx Ingress Controller or
     - can be HAProxy Ingress Controller
  3. Load Balancer ( HAProxy or Nginx )
</pre>

Let's there is a home for Tektutor website

Ingress (Routing rule)
Home Page - http://www.tektutor.org
Rule 1 - http://www.tektutor.org/trainings
  - this should be forwarded to training service (openshift service - clusterip,nodeport,loadbalancer)
Rule 2 - https://www.tektutor.org/logout
  - this should be forwarded to logout service (openshift service - clusterip,nodeport,loadbalancer)

Ingress Controller has to pick the above Ingress rules, and it will configure a Load Balancer 

## Lab - Understanding Ingress
You need to find your openshift cluster domain and adjust the host url accordingly
```
oc describe ingresscontroller/default -n openshift-ingress-operator | grep Domain:
```

Expected output
<pre>
oc describe ingresscontroller/default -n openshift-ingress-operator | grep Domain:
  Domain:                  apps.ocp4.tektutor.org.labs  
</pre>

```
cd ~/openshift-april-2024
git pull
cd Day2/ingress

oc apply -f nginx-deploy.yml
oc apply -f hello-deploy.yml
oc get deploy,rs,po

oc apply -f nginx-clusterip-svc.yml
oc apply -f hello-clusterip-svc.yml
oc get svc

oc apply -f ingress.yml
oc get ingress
oc describe ingress/tektutor

curl http://tektutor.apps.ocp.tektutor.org.labs/nginx
curl http://tektutor.apps.ocp.tektutor.org.labs/hello
```
