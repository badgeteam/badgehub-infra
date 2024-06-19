# badgehub-infra

This repository contains configuration for the BadgeHub infrastructure. Currently in development.

We have several roles, which try to be separate of eachother so they may be used in a modular sense. 
## Roles 

### system

Installs base packages, configures time and journald logging 

### user

Creates the user and it's initial groups. Will also add ssh key auths. The password for the user is asked at the start of the script.

### ufw 

We use ufw for firewalling. A openssh rule is added by default via ansible. Due to how Docker and iptables interact it is not possible currently by default to block docker exposed ports. Exposed ports should be used only when needed. More at https://github.com/chaifeng/ufw-docker/blob/master/ufw-docker. 

### docker

Docker role for installing docker. This also includes a docker system prune service and custom logging driver. By default, the docker prune service runs every day at 4AM host time.

## Getting started

The playbook is designed to be ran from another host. First you must have ansible installed. 

To start the configuration of the Debian VM, you must first configure the VM interactievly. For convenience, there is a Debian preseed file included. This file can be loaded pre-boot which automatically provisions the VM so it's ready to log in. Some notes about it:

- It is configured to use DHCP at start
- It will create a single partition with LVM with swap
- It uses default credentials, which should be changed after installed.
- It runs sshd on start

Before you start, you should:

- Generate a public key pair and put it in public_keys/ directory in the root of the repo.
- Change the group_vars/all variables as per customizaiton needs 
- Generate a hosts.ini file which has the host information, follow example

You can start the playbook via:


```
 ansible-playbook -i hosts.ini -k playbook.yaml --ask-become-pass
```

