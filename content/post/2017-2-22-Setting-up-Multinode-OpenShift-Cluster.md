---
title: Setting up Multinode OpenShift Cluster
date: 2017-02-22
---

[OpenShift](https://www.openshift.org) is Red Hat's PaaS (Platform-as-a-Service) that allow developers to quickly develop, host, and scale applications in cloud.
Based on top of Docker containers and the Kubernetes container cluster manager, OpenShift adds developer and operational centric tools to enable rapid application development, easy deployment and scaling, and long-term lifecycle maintenance for small and large teams and applications.

In this blog, we will be setting up a Multinode OpenShift Cluster (Single Master with two nodes) and will deploy a sample application on it.
We need four machines for this setup, one for Master, two for nodes and a host machine to for the installation using Ansible.

##### Install Ansible and its depenedencies  on the host machine  

`$ sudo yum install -y ansible pyOpenSSL python-cryptography python-lxml`

##### Next clone the OpenShift Ansible repo on your host machine

```
$ sudo yum install -y git
$ mkdir $HOME/openshift
$ git clone https://github.com/openshift/openshift-ansible $HOME/openshift
```  

#### Edit the Ansible hosts file

`$ vim /etc/ansible/hosts`

```
# Create an OSEv3 group that contains the masters and nodes groups
[OSEv3:children]
masters
nodes

# Set variables common for all OSEv3 hosts
[OSEv3:vars]
# SSH user, this user should allow ssh based auth without requiring a password
ansible_ssh_user=<user>
ansible_become=true

# If ansible_ssh_user is not root, ansible_sudo must be set to true
ansible_sudo=true

#product_type=openshift
#deployment_type=enterprise (Require RH subscription)
deployment_type=origin

# uncomment the following to enable htpasswd authentication; defaults to DenyAllPasswordIdentityProvider
#openshift_master_identity_providers=[{'name': 'htpasswd_auth', 'login': 'true', 'challenge': 'true', 'kind': 'HTPasswdPasswordIdentityProvider', 'filename': '/etc/openshift/openshift-passwd'}]

# host group for masters
[masters]
<master-ip>

# host group for nodes, includes region info
[nodes]
<master-ip>
<node1-ip>
<node2-ip>
```

**We need to make sure the host is able to ssh on master and all the nodes.**

For password authentication we will use `ssh-keys`, we need to create `ssh-keys` on the master and the two nodes and transfer it to the host machine so the Ansible is able to remotely login without password and install the packages.

Run this command on Master and the two nodes to create the `ssh-keys` and transfer it to the host.

`$ ssh-keygen` 

`$ ssh-copid <host-name>@<host-ip>`

**`sshcopy-id` copies the public key of your default identity to the remote host. Remember to run the command on Master and all the nodes.**

##### Now we have everything ready to run the Ansible-Playbook.

```
$ cd $HOME/openshift/openshift-ansible
$ ansible-playbook playbooks/byo/config.yml
```

If everything goes well the installation will start and it will take some time to finish depending upon the internet speed.

After the installtion is done, login to the master to check the nodes connected to it.

`$ ssh <master-ip>`

`$ oc get nodes`
