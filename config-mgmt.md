# Configuration Management

## Common Tools
* Ansible
* Chef
* Puppet
* Salt

## Intro
Manage configuration for Applications

easy:
* nginx/apache/haproxy/sshd/...
* install/update packages

advanced:
* rolling update (blue/green deployment)
* complex workflows aka playbooks (e.g. restore DB backup)

also:
* dynamic information gathering (what hosts are there? IPs? state of packages?)
* secrets management (CA/ssh-keys/API keys/...)
* run one-off jobs like "create-db-backup" etc.

## Config Management Paradigms

### Mutable infrastructure
* push-based provisioning
* pull-based provisioning
=> agent+master vs. agentless (-> bootstrapping problem & security)
=> configuration drift


### Immutable Infrastructure (not configuration management)
* Immutable Artifacts (Image, Container)
=> every change is a new artifact (new version)
=> different tooling: Packer/Terraform/CloudFormation


### Multiple Tools
e.g. Terraform + Ansible


### Workflow
From CI/CD Pipeline:
* create Infrastructure w/ terraform (VMs, Network, ...)
* provision machine configuration
* run tests


## Lab Assignments
We're using Ansible. Create two VMs and a ssh-key.

##### Prerequisites:
Provision the VMs with your ssh public key and install the necessary software on the VMs. You will see how repetitive and error-prone this task is.

Ansible Installation Guide: https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

##### Task
* the first node acts as a load-balancer
  * create a role or use a role from ansible galaxy to install an nginx webserver on this node
  * nginx should listen on port 80 and forward the traffic to the application server on port 8888
* the second node is your application server
  * install docker
  * start a docker container cilium/echoserver:latest
  * publish the containers port 8080 to the host on port 8888 (VM:8888 -> container:8080)
  * start the container with a environment variable POD_NAME. It should have the value of the VM hostname

Testing:
* test if you can connect to the application server through the load-balancer
* add a test at the end of your playbook

##### Hardening assignments
* are the VMs reboot safe? (make it reboot-safe if not)
* can you safely kill the process in the echoserver container? does the container come up again? (make container automatically restart)

##### Extra assignments
1. blue-green deployment
* add another VM to your inventory: this is a 2nd application server (re-use the role of your application server)
* create a playbook that does a blue-green deployment

2. https
* make nginx listen on :443 aswell
* create a self-signed certificate locally and provision it on the load-balancer
* the lb -> app server connection does not need to be encrypted (in this lab)

3. How do you automate the provisioning of your VMs from the `prerequisites` ?
