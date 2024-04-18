# Day 3

## Info - Deployment vs DeploymentConfig

- In older version of Kubernetes, to deploy applications, we had to use ReplicationController
- ReplicationController supports
  - Scale up/down
  - Rolling update
  - it doesn't support declarative application deployment
  - during this time, OpenShift introducted a new type of Resource called DeploymentConfig to deploy applications, deploymentconfig pretty much does what Deployment and ReplicaSet does today
  - meanwhile, Kubernetes new versions introducted Deployment and Replicaset as an alternate to ReplicationController
  - Deployment & ReplicaSet
    - supports scale up/down
    - supports rolling update
    - its supports creating deployment in imperative and declarative style
    - in new version of K8s and OpenShift, it is recommended to use Deployment over the DeploymentConfig
  - ReplicationController and Deploymentconfig are deprecated in Openshift but exists for backward compatibility

## Installing Helm Kubernetes/OpenShift Package Manager
Helm is already installed on Server1, Server2 and Server 3 for all users. Hence, the below instructions are just for your reference. This requires administrator privilege on the linux machine.

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

# Bonus Labs (Optional - not in our training agenda)

## Lab - Finding more details about Openshift private image registry
Red Hat Openshift comes with a private Container Registry out of the box.  You may try extracting more details about the openshift image registry as shown below

```
oc describe svc/image-registry -n openshift-image-registry
```

Expected output
<pre>
[jegan@tektutor.org openshift-april-2024]$ oc describe svc/image-registry -n openshift-image-registry
Name:              image-registry
Namespace:         openshift-image-registry
Labels:            docker-registry=default
Annotations:       imageregistry.operator.openshift.io/checksum: sha256:1c19715a76014ae1d56140d6390a08f14f453c1a59dc36c15718f40c638ef63d
                   service.alpha.openshift.io/serving-cert-secret-name: image-registry-tls
                   service.alpha.openshift.io/serving-cert-signed-by: openshift-service-serving-signer@1710727234
                   service.beta.openshift.io/serving-cert-signed-by: openshift-service-serving-signer@1710727234
Selector:          docker-registry=default
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                172.30.193.120
IPs:               172.30.193.120
Port:              5000-tcp  5000/TCP
TargetPort:        5000/TCP
Endpoints:         10.128.0.38:5000
Session Affinity:  None
Events:            <none>  
</pre>

## Lab - In case you are curious to see how does the etcd key/value data-store stores the data in openshift
In the below commands, replace 'jegan' with your project name(your name)

```
oc project openshift-etcd
oc rsh po/etcd-master-1.ocp4.tektutor.org.labs
etcdctl get "" --keys-only --prefix=true
etcdctl get "" --keys-only --prefix=true | grep jegan
etcdctl get "/kubernetes.io/deployments/jegan/mariadb" --prefix=true
etcdctl get "/kubernetes.io/pods/jegan/mariadb-8469c94c8b-tf65s" --prefix=true
```

## Lab - Deploying a multipod java application that fetch data from mariadb database
![Multi-Pod application](hello-ms-multipod.png)

```
cd ~/openshift-april-2024
git pull

cd Day3/hello-microservice
oc apply -f configmap.yml
oc apply -f secrets.yml
oc apply -f mariadb-pv.yml
oc apply -f mariadb-pvc.yml
oc apply -f mariadb-deploy.yml
oc apply -f mariadb-svc.yml

oc apply -f openshift-helloms-deploy.yml
oc apply -f openshift-helloms-svc.yml
oc apply -f openshift-helloms-route.yml
```

You can connect to mariadb pod shell as shown below, when it prompts for password type 'root@123'
```
oc rsh pod/mariadb-7889ddc665-9kskb
mysql -u root -p
CREATE DATABASE tektutor;
USE tektutor;
CREATE TABLE greeting ( message VARCHAR(100) NOT NULL );
INSERT INTO greeting VALUES ( "Hello Microservice 1.0 !" );
SELECT * FROM greeting;
```


Now you should be able to access the openshift helloms route from cli or web browser.  You need to use your route url which might look like http://openshift-hello-ms-<your-name>.apps.ocp4.rpsconsulting.in
```
oc get route
curl http://openshift-hello-ms-jegan.apps.ocp4.tektutor.org.labs
```

## Lab - Deploying multipod wordpress application
You need to update mariadb-pv.yml, mariadb-pvc.yml, mariadb-deploy.yml, wordpress-pv.yml wordpress-pvc.yml and wordpress-deploy.yml before applying.

```
cd ~/openshift-april-2024
git pull

cd Day3/wordpress
./deploy.sh
```

You can access the wordpress from Developer Context --> Topology and click on the route(up arrow) that appears on the wordpress deployment.  You are supposed to see the wordpress blog page.

Once you are done with this lab exercise, it is recommended to delete the wordpress deployment to free up the resources

```
cd ~/openshift-april-2024
git pull

cd Day3/wordpress
./delete.sh
```

## Lab - Creating a daemonset

This will create one nginx pod per node automatically. If additional nodes are added to the openshift cluster, the daemonset controller will automatically create a new pod and assign it on the new node, on the similar fashion if a node is removed from the cluseter, the daemonset pod get deleted automatically from the node.

Example for Daemonset
- kube-proxy that supports load-balancing functionality to the pods behind Service has to be deployed on each node ( One kube-proxy per node )
- CoreDNS pod that supports service discovery functionality has to be deployed on each node ( one pod per node )
- Prometheus pod that collects performance metrics has to be deployed on each node ( one pod per node )

We will try to deploy one nginx pod per node by creating the below daemonset
```
cd  ~/openshift-april-2024
git pull

cd Day3/daemonset
oc apply -f nginx-ds.yml
```

Expected output
<pre>
[root@tektutor.org Day3]# <b>cd daemonset/</b>
[root@tektutor.org daemonset]# <b>ls</b>
nginx-ds.yml
  
[root@tektutor.org daemonset]# <b>oc apply -f nginx-ds.yml</b>
daemonset.apps/nginx created
  
[root@tektutor.org daemonset]# <b>oc get po</b>
NAME          READY   STATUS              RESTARTS   AGE
nginx-f29f6   0/1     ContainerCreating   0          2s
nginx-ffsqw   0/1     ContainerCreating   0          2s
nginx-hgrdb   0/1     ContainerCreating   0          2s
nginx-r97pv   0/1     ContainerCreating   0          2s
nginx-zstvs   0/1     ContainerCreating   0          2s
  
[root@tektutor.org daemonset]# <b>oc get po -w -o wide</b>
NAME          READY   STATUS              RESTARTS   AGE   IP             NODE                              NOMINATED NODE   READINESS GATES
nginx-f29f6   0/1     ContainerCreating   0          7s    <none>         master-1.ocp4.tektutor.org.labs   <none>           <none>
nginx-ffsqw   1/1     Running             0          7s    10.131.0.82    worker-2.ocp4.tektutor.org.labs   <none>           <none>
nginx-hgrdb   1/1     Running             0          7s    10.128.2.17    worker-1.ocp4.tektutor.org.labs   <none>           <none>
nginx-r97pv   0/1     ContainerCreating   0          7s    <none>         master-3.ocp4.tektutor.org.labs   <none>           <none>
nginx-zstvs   1/1     Running             0          7s    10.129.0.218   master-2.ocp4.tektutor.org.labs   <none>           <none>
nginx-f29f6   1/1     Running             0          15s   10.128.0.217   master-1.ocp4.tektutor.org.labs   <none>           <none>
nginx-r97pv   1/1     Running             0          15s   10.130.0.68    master-3.ocp4.tektutor.org.labs   <none>           <none>
  
^C[root@tektutor.org daemonset]# <b>oc get po</b>
NAME          READY   STATUS    RESTARTS   AGE
nginx-f29f6   1/1     Running   0          18s
nginx-ffsqw   1/1     Running   0          18s
nginx-hgrdb   1/1     Running   0          18s
nginx-r97pv   1/1     Running   0          18s
nginx-zstvs   1/1     Running   0          18s
  
[root@tektutor.org daemonset]# <b>cat nginx-ds.yml</b>
apiVersion: apps/v1
kind: DaemonSet
metadata:
  labels:
    app: nginx
  name: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: bitnami/nginx:latest
        name: nginx
</pre>

## Setup your trial JFrog Artifactory to configure OpenShift Private Container Registry
```
https://jfrog.com/start-free/
```

With your gmail, you can create 14 days trial.
