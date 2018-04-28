---
title: Installing Minikube
date: 2016-12-16
---

**Kubernetes** is a container orchestration tool developed by Google, written completely in Golang. It provides a platform for deployment, scaling and automating your application in a containerized environment. Kubernetes helps you scale up your application with ease and make deployment quick. Containerize an application by creating Docker config files and build processes to produce all the necessary Docker images Configure and launch an auto-scaling, self-healing Kubernetes cluster. 
It is sometimes very difficult to set up a Kubernetes cluster bit thanks to `minikube` we can set it it up within few commands so that you can run a single node Kunbernetes cluster locally.

### What is minikube?

**minikube** is tool that gives you the ability to run a single node Kubernetes cluster locally inside a Virtual Machine. It supports a number of hypervisers like KVM and VirtulBox for Linux, Hyper-V for Windows and xhyve driver for OS-X. It supports a variety of Kubernetes features like ConfigMaps, Secrets, NodePort and Dashboard.
We will be using `minikube` for Linux in this tutorial.

### Prerequisites for installing minikube

1. VirtualBox should be installed on your system.
    To install VirtualBox on CentOS

    `$ yum install VirtualBox-5.1`

2. **kubectl** should be in your PATH.

    `kubectl` is a command line tool which is used to interact with Kubernetes cluster from command line.
   
    Download `kubectl` and put it in your PATH.

```bash
    $ wget https://storage.googleapis.com/kubernetes-release/release/v1.4.4/bin/linux/amd64/kubectl
    $ chmod +x kubectl
    $ mv kubectl /usr/local/bin/kubectl
    $ kubectl 
    kubectl controls the Kubernetes cluster manager.
    
    Find more information at https://github.com/kubernetes/kubernetes.
    
    Usage:
      kubectl [flags]
      kubectl [command]
```

   `kubectl` will provide you a bunch of commands and it will handy if we enable bash completion. The bash completion script is generated automatically you just need to add it in your **~/.bashrc**

```bash
    $ echo "source <(kubectl completion bash)" >> ~/.bashrc`
    $ source ~/.bashrc
```
    
### Installing minikube

```bash
    $ curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.14.0/minikube-linux-amd64
    $ chmod +x minikube 
    $ sudo mv minikube /usr/local/bin/
```
  
  That's it `minikube` is now installed and is in your PATH.
  
### Starting minikube

```bash
     $ minikube start
     Starting local Kubernetes cluster...
     Kubectl is now configured to use the cluster.
```     

  Here we are using VirtualBox as our hypervisor but, if you want to switch to different hypervisor use `--vm-flag=<hypervisor>` eg. `--vm-flag=kvm`
  
```bash
    $ minikube --vm-driver=kvm start
      Starting local Kubernetes cluster...
      Kubectl is now configured to use the cluster.
```

  Now we have full-blown single node Kubernetes cluster running inside a virtual machine. Don't worry about the system resources used by the VM since, is a boot2docker ISO which is a lightweight Linux distribution of Core OS running completely on RAM, made specially to run docker container.
  
  All the deployment will be done inside the VM, we need not to ssh inside the VM, but we can using `minikube ssh`

```bash
      $ minikube ssh
                            ##         .
                      ## ## ##        ==
                   ## ## ## ## ##    ===
               /"""""""""""""""""\___/ ===
          ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
               \______ o           __/
                 \    \         __/
                  \____\_______/
     _                 _   ____     _            _
    | |__   ___   ___ | |_|___ \ __| | ___   ___| | _____ _ __
    | '_ \ / _ \ / _ \| __| __) / _` |/ _ \ / __| |/ / _ \ '__|
    | |_) | (_) | (_) | |_ / __/ (_| | (_) | (__|   <  __/ |
    |_.__/ \___/ \___/ \__|_____\__,_|\___/ \___|_|\_\___|_|
    Boot2Docker version 1.11.1, build master : 901340f - Fri Jul  1 22:52:19 UTC 2016
    Docker version 1.11.1, build 5604cbe
```

### Access minikube dashboard

   `$ minikube dashboard`
 
![Image](https://github.com/procrypt/files/blob/master/minikube-dashboard.png?raw=true)

That's it! Now you can use `kubectl` to deploy your application.

### Stop minikube cluster

```bash
   $ minikube stop
     Stopping local Kubernetes cluster...
     Machine stopped.
```     
