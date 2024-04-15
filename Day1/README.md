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
