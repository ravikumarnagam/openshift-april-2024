# Day 1

## Troubleshooting RPS Cloud Login
```
- The user-id that was shared to you seems to miss letter 'u', so you need to type your username as 24MAN0113-u25.
- The last two digit number in the username would vary for each participant.
```

## About our lab environment

There are total 3 linux servers
```
Server 1 - 10.10.15.60 (user01 - user09)
Server 2 - 10.10.15.63 (user10 - user17)
Server 3 - 10.10.15.64 (user18 - user25)
```

The linux login credentials ( RDP Connection link that you see on the RPS Windows cloud machine )
In case your cloud login username is 24MAN0113-u25, then you should login as user25 with password redhat to the Server 3 (10.10.15.64).
In case you cloud login username 24MAN0113-u15, then you should login as user15 with password 'redhat' to the server 2 (10.10.15.63).


## Pre-test - kindly complete the pre-test from your RPS Lab machine
<pre>
https://app.mymapit.in/code4/tiny/8Y8cj9
</pre>

Note
<pre>
- Copy/Paste between your laptop and rps lab machine is disabled as per your bank policy
- You don't have to enable the camera access
- Kindly use your personal email-id while registering ( avoid using BOFA id )
- The link above may not work directly from your work laptop web browser
- Once you are done with the test we will start the training 
- Kindly leave a message via WebEx chat if you have access, otherwise you may tell me
</pre>

## Verifying your Linux lab machine

Check if docker community edition is pre-installed
```
docker --version
```

Expected output
<pre>
[root@tektutor.org openshift-april-2024]# docker --version
Docker version 26.0.1, build d260a54
</pre>

Check the version of oc and kubectl openshift client tools
```
oc version
kubectl version
```

Expected output
<pre>
[root@tektutor.org openshift-april-2024]# <b>oc version</b>
W0415 10:23:48.403940 1673608 loader.go:222] Config not found: /root/ocp4_cluster_ocp/install_dir/auth/kubeconfig
Client Version: 4.14.12
Kustomize Version: v5.0.1
  
[root@tektutor.org openshift-april-2024]# <b>kubectl version</b>
W0415 10:23:52.446759 1673707 loader.go:222] Config not found: /root/ocp4_cluster_ocp/install_dir/auth/kubeconfig
WARNING: This version information is deprecated and will be replaced with the output from kubectl version --short.  Use --output=yaml|json to get the full version.
Client Version: version.Info{Major:"1", Minor:"27", GitVersion:"v1.27.4", GitCommit:"286cfa5f978c4a89c776347c82fa09a232eef144", GitTreeState:"clean", BuildDate:"2024-01-29T22:50:23Z", GoVersion:"go1.20.12 X:strictfipsruntime", Compiler:"gc", Platform:"linux/amd64"}
Kustomize Version: v5.0.1  
</pre>

Check if you are able to list out the nodes in the Openshift cluster
```
oc get nodes
```
Expected output
<pre>
[root@tektutor.org openshift-april-2024]# <b>oc get nodes</b>
NAME                              STATUS   ROLES                         AGE     VERSION
master-1.ocp4.tektutor.org.labs   Ready    control-plane,master,worker   5h35m   v1.27.11+749fe1d
master-2.ocp4.tektutor.org.labs   Ready    control-plane,master,worker   5h35m   v1.27.11+749fe1d
master-3.ocp4.tektutor.org.labs   Ready    control-plane,master,worker   5h35m   v1.27.11+749fe1d
worker-1.ocp4.tektutor.org.labs   Ready    worker                        5h18m   v1.27.11+749fe1d
worker-2.ocp4.tektutor.org.labs   Ready    worker                        5h18m   v1.27.11+749fe1d  
</pre>

## Processors
- AMD/Intel they support multiple CPU cores in a single Processor
- These days, processors comes in different packaging
  - SCM (Single Chip Module) - one IC will host one Processor
  - MCM (Multiple chip Module) - one IC will host many Processors
- It is possible a single IC may support 2/4/8 Processors
- Server grade motherboards generally support many Processor Sockets
- Assume a server with 4 Sockets, each Socket is installed with a MCM Processor ( 4 Processor/socket) - a total of 16 Processors
- assume each Processor supports 128 Cores
- 16 x 128 = 2048 Physical CPU Cores
- 2048 x 2 = 4096 virtual/logical CPU Cores ( 1 Server )
- 1 Server can technically host 1000 virtual machines(Operating system)
- this kind of virtualization is called heavy-weight virtualization
  - because for each VM, we need to allocate dedicated hardware resources
    - CPU Cores
    - RAM
    - Disk/Storage 
Hyperthreading
- each physical cpu cores is seen as 2/4 virtual cores

## Hypervisor ( software + hardware )
- refers to virtualization technology
- through virtualization, we can run many OS side by side on the same physical machine(laptop/desktop/workstation/server)
- each Virtual machines refers to one fully functional operating system
- in other words, many OS can be active at the same time within a single machine
- Processor on your laptop/desktop/workstation/server should support virtualization
  - Intel (VT-X - Virtualization Feature)
  - AMD (AMD-V - Virtualization Feature)
- there are two types of Hypervisors available
  - Type 1 ( Used in Servers/Workstations - Bare-metal - don't need OS )
  - Type 2 ( Used in laptop/desktops - can be installed only top of a OS [Windows,Linux,Mac] )
- Examples
  - Type 1 Baremetal Hypervisors
    - VMWare vSpere/vCenter
  - Type 2 Hypervisors
    - VMWare Workstation (Linux,Windows)
    - VMWare Fusion (Mac OS-X)
    - Oracle VirtualBox
    - Microsoft Hyper-V
    - Parallels ( Mac OS-X)
    - KVM (Linux)

What is the minimum number of physical servers required to support 1000 Virtual Machine(VM - OS)?


## Container Overview
- an application virtualization technology
- each container represents one single application or application process
- container will host the application and its dependent libraries/software utilities, etc
- containers - there are similarities with virtual machines
  - just like virtual machines has shell, even a container may have its own shell like bash/sh,etc.,
  - just like virtual machines has their own network stack, containers also has their own network stack
  - just like virtual machines has atleast one IP address, containers also get atleast one IP address
  - just like virtual machine has its own file system, containers also has their own files system(files/folders)
- containers that runs in the same machines shares the hardware resources on the underlying operating system
- containers don't represent a OS, containers don't have their own hardware or OS kernel
- containers depends on the underlying os kernel for any OS functionality

## Container Engine
- is a high-level software that offers easy to use user-friendly commands
- without knowing low-level linux kernel features, we can easily manage images and containers
- hence, end-users tend to use Container Engines over the Container Runtime
- Container Engines interally depends on Container Runtimes to manage images and containers
- Examples
  - Docker is a Container Engine ( depends on runC Container Runtime )
  - Podman is a Container Engine ( depends on CRI-O Container Runtime )

## Container Runtime
- is a low-level software that manages images and containers
- it is not so user-friendly, hence generally end-users like us won't directly use a container runtime
- know how to
  - create a container using container image
  - stop/restart/kill/abort containers
- Examples
  - runC
  - CRI-O
  
## Linux kernel features that enable container technology
- Namespace ( containers running on the same machines are isolated from each other )
- Control Groups (CGroups)
  - this allows applying resource quota restrictions on containers
    - we can restrict how much percentage of CPU resources can be used by a container at the max
    - we can restrict how much RAM/storage a container can use at the max

## Is it possible to install Docker on Windows/Mac?
- Yes it is possible to install Docker in Windows or Mac
- When we install Docker for Windows/Mac, it also installs a thin linux-layer(Linux kernel)
- technically linux containers runs on top of Linux even in a Windows/Mac machines

## Is it possible to run .Net application within container?
- Yes it is possible
- MONO - is linux implemention of .Net Framework specification
- MONO supports running .Net applications on Linux too

## What is a Docker Image?
- it is like an ISO image we use to install Windows or Mac or Linux
- in other words, it is specification of a Docker container
- in other words, it is a blueprint of a Docker container
- using a Docker Image, we can create any number of containers
- each Container Image has an unique ID and name

## What is a Docker Container?
- Container is a running instance of a Docker image
- each container has an unique ID and unique name
- each container has an IP address, hostname, etc.,
- every container has it own port range 0 - 65535 ( it uses a port namespace )
  
## What is a Docker Container Registry?
- it is a collection of many Docker Images
- Docker supports 3 types of Registries
  1. Local Docker Registry ( it is a folder /var/lib/docker on linux )
  2. Private Docker Registry ( Sonatype Nexus or JFrog Artifactory )
  3. Docker Hub Website or Remote Registry powered by Sonatype Nexus Server

## What is a Container Orchestration Platform?
- Container Orchestration Platforms manages containers application workloads
- though we can manually create, start, stop,restart, delete containers, practically in the industry no one manages containers manually
- they offer high-availability (HA) to your containerized applications
- they offer in-built monitoring features to check health/liveliness,etc.,
- they offer in-built load balancing
- they allow exposing your applications only within your network or externally to internet via Services
- they support service discovery
- they allow scaling up/down your application workloads
- they allow upgrading/downgrading your application from one version to the other without any downtime
- Examples
  - Docker SWARM
  - Google Kubernetes
  - Red Hat OpenShift

## About Docker SWARM
- Docker Inc is the organization that developed Docker Container Engine
- Docker SWARM is Docker Inc's native orchestration platform
- Docker SWARM only supports Docker based containers
- it is not production grade container orchestration platform
- it is very light-weight, hence can be easily installed in your laptop
- it is very good for learning container orchestration concepts in general, also good for light-weight developer/qa setup but not generally used in production

## About Google Kubernetes
- open source
- production grade
- supports many different types of containers ( containerd, LXC, CRI-O, etc,,)
- supports extending Kubernetes APIs, by adding Custom Resources and Custom Controllers
  
## About Red Hat Openshift
- production grade
- paid software from Red Hat ( an IBM company )
- it is a Red Hat's distribution of Kubernetes
- it is developed on top of opensource Google Kubernetes, hence it is a super set of Google Kubernetes with some additional features
- Red Hat OpenShift has added many Customer Resources and Custom Controllers they added additional features on top of Google Kubernetes
- Unlike Kubernetes, OpenShift only supports CRI-O containers ( Podman Container Engine )
- OpenShift upto v3.x it was using Docker (runC) containers only
- OpenShift v4.x onwards it stopped support for Docker and replaced Docker with Podman(CRI-O)

## Red Hat OpenShift Architecture

## Master Node - Control Plane Components
- Control Plane Components runs only on the master nodes
- Control Plane Components
  - API Server
  - etcd key/value datastore
  - Scheduler
  - Controller Managers ( Collection of many Controllers )
    - Deployment Controller
    - ReplicaSet Controller
    - ReplicationController
    - Job Controller
    - CronJob Controller
    - Endpoint Controller
    - StatefulSet Controller
    - Daemonset Controller

## API Server
- a collection of many REST APIs
- for every features supported by Openshift there is a bunch REST APIs
- is the heart of openshift
- all the Kubernetes/Openshift components only communicate to API server via REST calls
- API server notifies about different changes to other components in K8s/Openshift via events
- API server is the only component which stores/fetches data into the etcd database
## Scheduler

## etcd database
- key/value based data store
- it is an opensource database which can be used outside the scope of Kubernetes/Openshift
- this is where the entire cluster-state, application status, etc are stored
- each time some change happens in etcd database, API server will notify  other components about those changes via broadcasting events
  
## Controller Managers
- for every type of resource there is one controller to manage them
- controllers supports monitoring features
- they check the health of applications/resources and repairs them when required
- Whenever application is deployed via Deployment, the Deployment Controller is the one which creates the Pods for a deployment, monitors the health of the Pod and when required heals/repairs/replaces bad pods with good healthy ones

## Kubernetes/Openshift resources
- Deployment
- ReplicaSet
- Pod
- StatefulSet
- DaemonSet
- Job
- CronJob
- Service

## Kube config file
- the oc/kubectl client tools requires a config file that has connection details to the API Server(load balancer)
- the config file is generally kept in user home directory, .kube folder and the default name of kubeconfig is config
- optionally we could also use the --kubeconfig flag with the oc command to point to a config file
- it is also possible to use a KUBECONFIG environment variable to point to the config file
- Just to give an idea, it is possible that your Kubernetes/OpenShift is running in AWS/Azure but you could install oc/kubectl client tool on your laptop with a config file and still run all the oc/kubectl commands from your laptop without going to aws/azure

To print the content of kubeconfig file
```
cat ~/.kube/config
```
