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

