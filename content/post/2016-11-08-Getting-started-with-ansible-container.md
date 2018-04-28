---
title: Getting started with Ansible Containers
date: 2016-11-08
---

Ansible Container is the ultimate workflow for container development, testing, and deployment.

Ansible Container enables you to build Docker images and orchestrate containers using only Ansible playbooks. Describe your application in a Docker Compose-like format and, rather than using a Dockerfile, provide a playbook with tasks for building images. Ansible Container will take it from there.

With Ansible Container, you are no longer limited by Dockerfile. You can now apply the power of Ansible to the image build process. Use templates, copy files, drop in encrypted data, handle errors, add conditionals, and more. Everything Ansible brings to orchestrating your infrastructure can now be applied to the image build process.

But Ansible Container does not stop there. Use Ansible Container to run the application, and push images to private and public registries. When you are ready to deploy to the cloud, use it to generate an Ansible role that automates the deployment.


## Work-Flow
`$ ansible-container init`

Creates a directory ansible with files to get you started. 

`$ ansible-container build`

Creates images from your Ansible playbooks.

`$ ansible-container run`

Launches the containers specified in the orchestration document, container.yml, for testing the built images. The format of container.yml is nearly identical to Docker Compose.

`$ ansible-container push`

Pushes the project's images to a container registry of your choice.

`$ ansible-container shipit`

Generates the necessary playbook and role to deploy your containers on a supported cloud provider viz. OpenShift and Kubernetes.

## Installation

#### Requirements:

Python 2.7, pip

`$ sudo pip install ansible-container`

#### Wordpress-Mariadb Example

- A Wordpress-Mariadb example built from scratch on Centos 7.
- Creates a Mariadb container and a Wordpress container.
- Demonstrates deployment to Kubernetes and OpenShift.

This is what `main.yml` looks like.

```yaml
-  hosts: db
   vars:
     - wp_mysql_db: wordpress
     - wp_mysql_user: wordpress
     - wp_mysql_password: password

-  hosts: wordpress
   vars:
     - wp_mysql_db: wordpress
     - wp_mysql_user: wordpress
     - wp_mysql_password: password
```

We can change the user and database by editing the main.yml file.

```yaml
-  hosts: db
   vars:
     - wp_mysql_db: < database name >
     - wp_mysql_user: < user name >
     - wp_mysql_password: < password >
     
-  hosts: wordpress
   vars:
     - wp_mysql_db: < database name >
     - wp_mysql_user: < user name >
     - wp_mysql_password: < password >
```

#### Build Images

```
$ cd example/wordpress-example
$ ansible-container build
```

#### Run the Container

```
$ ansible-container run
```

#### Push images to cloud

```
$ docker login
$ ansible-container push
```

#### Deploy to OpenShift

```bash
$ ansible-container shipit openshift --save-config

Images will be pulled from procrypto
Attaching to ansible_ansible-container_1
Cleaning up Ansible Container builder...
Role wordpress created.
```

It will create the OpenShift artifacts to deploy it on OpenShift.


#### Run the OpenShift artifacts

```bash
$ oc create -f wordpress/ansible/shipit_config/openshift/

route "db-3306" created
deploymentconfig "db" created
service "db" created
route "wordpress-80" created
deploymentconfig "wordpress" created
service "wordpress" created
```

#### View the Services and Deployments on OpenShift

```bash
$ oc get service
NAME        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
db          172.30.32.159   <none>        3306/TCP   3h
wordpress   172.30.47.102   <none>        80/TCP     3h
```

#### Next, take a look at the pods created by the deployments

```bash
$ oc get pods
NAME                READY     STATUS    RESTARTS   AGE
db-1-0xob4          1/1       Running   0          3h
wordpress-1-9wp23   1/1       Running   0          3h
```

#### Details of one of the pods

```bash
$ oc describe pod wordpress-1-9wp23 
Name:		wordpress-1-9wp23
Namespace:	sample-project
Node:		adb.vm/192.168.121.210
Start Time:	Thu, 25 Aug 2016 01:58:28 -0400
Labels:		app=wordpress,deployment=wordpress-1,deploymentconfig=wordpress,service=wordpress
Status:		Running
IP:		172.17.0.6
Controllers:	ReplicationController/wordpress-1
Containers:
  wordpress:
    Container ID:	docker://49192ac035b01e56632a2090ee05695ffba54dc93ddfd45312486c4568775d56
    Image:		procrypto/wordpress-wordpress:20160822144236
    Image ID:		docker://8012c285060d102987cfbcc4b6a6220bc1037ab4d5b74b0de1568f43d6495f32
    Port:		80/TCP
    Args:
      bash
      -c
      bash /tmp/a.sh ; usr/sbin/apachectl -D FOREGROUND
    QoS Tier:
      memory:		BestEffort
      cpu:		BestEffort
    State:		Running
      Started:		Thu, 25 Aug 2016 02:01:57 -0400
    Ready:		True
    Restart Count:	0
    Environment Variables:
Conditions:
  Type		Status
  Ready 	True 
Volumes:
  default-token-4s5b8:
    Type:	Secret (a volume populated by a Secret)
    SecretName:	default-token-4s5b8
```


#### Deploy to Kubernetes

```bash
$ ansible-container shipit kube --save-config

Images will be pulled from procrypto
Attaching to ansible_ansible-container_1
Cleaning up Ansible Container builder...
Role wordpress created.
```

It will create the Kubernetes artifacts to deploy it on Kubernetes.


#### Run the Kubernetes Artifacts
```bash
$ kubectl create -f wordpress/ansible/shipit_config/kubernetes/

deployment "db" created
service "db" created
deployment "wordpress" created
service "wordpress" created
```

#### View the Services on Kubernetes

```bash
$ kubectl get service

NAME         CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
db           10.254.176.204   <none>        3306/TCP   1m
kubernetes   10.254.0.1       <none>        443/TCP    2d
wordpress    10.254.177.169   <none>        80/TCP     1m
```

#### View the Deployments

```bash
$ kubectl get deployment 
NAME        DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
db          1         1         1            1           4m
wordpress   1         1         1            1           4m
```

#### Next, take a look at the pods created by the deployments

```bash
$ kubectl get pods
NAME                         READY     STATUS    RESTARTS   AGE
db-1420940741-bluto          1/1       Running   0          1m
wordpress-1256450439-vqksz   1/1       Running   0          1m
```

#### Details of one of the pods

```bash
$ kubectl describe pod wordpress-1256450439-vqksz 
Name:		wordpress-1256450439-vqksz
Namespace:	default
Node:		127.0.0.1/127.0.0.1
Start Time:	Thu, 25 Aug 2016 05:58:12 -0400
Labels:		app=wordpress,pod-template-hash=1256450439,service=wordpress
Status:		Running
IP:		172.17.0.3
Controllers:	ReplicaSet/wordpress-1256450439
Containers:
  wordpress:
    Container ID:	docker://03136dc3b4aa204bc8f0e3c1811ed061ccb7b7f9bb8d3a6e1d7cd12c40f14767
    Image:		procrypto/wordpress-wordpress:20160822144236
    Image ID:		docker://8012c285060d102987cfbcc4b6a6220bc1037ab4d5b74b0de1568f43d6495f32
    Port:		80/TCP
    Args:
      bash
      -c
      bash /tmp/a.sh ; usr/sbin/apachectl -D FOREGROUND
    QoS Tier:
      cpu:		BestEffort
      memory:		BestEffort
    State:		Running
      Started:		Thu, 25 Aug 2016 05:58:19 -0400
    Ready:		True
    Restart Count:	0
    Environment Variables:
Conditions:
  Type		Status
  Ready 	True 
Volumes:
  default-token-61sic:
    Type:	Secret (a volume populated by a Secret)
    SecretName:	default-token-61sic
```
