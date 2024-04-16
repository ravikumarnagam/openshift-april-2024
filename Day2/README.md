# Day 2

## Lab - Deploying an application into openshift using declarative style(using yaml files)

Kubernetes/Openshift supports deploying and managing application in two style
1. Imperative style ( using commands )
2. Using source code/manifest files(yaml files)

Let's auto-generate yaml file to deploy nginx into openshift declaratively
```
oc create deploy nginx --image=bitnami/nginx --replicas=3 --dry-run=client -o yaml
oc create deploy nginx --image=bitnami/nginx --replicas=3 --dry-run=client -o yaml > nginx-deploy.yml
oc apply -f nginx-deploy.yml
oc get deploy,rs,po
```
