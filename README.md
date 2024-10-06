# badgehub-infra

This repository contains the configuration for the BadgeHub infrastructure. Currently in development.

## Overview

We have several roles designed to be modular, so they may be used independently. The Ansible playbooks will be designed to run but not limtied to:

- BadgeHub API
- Keycloak for authentication

## Goals

The goal is to create an easy and reproducible recipe to procure BadgeHub infrastructure that will reliably host the services needed for WHY2025 and any future events held by any other parts of the community.

## Requirements

### Software

- Ansible core 2.17.1 minimum
- Debian target host with internet access

### Recommended Hardware Specifications

There is currently no real world test case on how many resources does the server need as it is in early development. A good guesstimate is:

#### For Test VM:

- 2 vCPU minimum
- 2-4GB RAM minimum
- 20-30GB disk space

#### For Actual Server:

- 4 vCPU minimum
- 4GB RAM minimum
- 50GB disk space

## Playbook Variables

Some key variables to look after:

- `update_system: true` - Update system packages if first install or when needed
- `update_docker: true` - Update Docker
- `createorupdate_main_user: false` - Update user if any changes needed

We also have environment specific variables in for each host group in `group_vars/`.

## Contribution Guide

At the moment, one recommendation is to use `ansible-lint` to lint the playbooks. You may create a fork with a different branch name and then create a PR explaining the changes. For any other questions or issues, you may open an issue or discussion.

## Roles

### System

Installs base packages, configures time and journald logging.

### User

Creates the user and its initial groups. Will also add SSH key authentication. The password for the user is asked at the start of the script.

### UFW

We use UFW for firewalling. An OpenSSH rule is added by default via Ansible. Due to how Docker and iptables interact, it is currently not possible to block Docker-exposed ports by default. Exposed ports should be used only when needed. More details at [ufw-docker](https://github.com/chaifeng/ufw-docker/blob/master/ufw-docker).

### Docker

Role for installing Docker. This also includes a Docker system prune service and custom logging driver. By default, the Docker prune service runs every day at 4 AM host time.

### Filesystem

Currently, it just creates a directory for the Docker Compose app. Future enhancements may be added.

### SSH

Adds public keys and configures `sshd` with sane defaults.

### BadgeHub API

Creates needed directories to deploy and will clone the repository.

## Getting Started

The playbook is designed to be run from another host. First, you must have `ansible-core` and `ansible` installed, tested with at least `ansible-core 2.17.1` on ArchLinux but the host OS should not matter.

### Setting Up the Environment

To start the configuration of the Debian VM, you must first configure the VM interactively. For convenience, there is a Debian preseed file included. This file can be loaded pre-boot to automatically provision the VM so it's ready to log in and configure.

Some notes about it:

- Configured to use DHCP at start
- Creates a single partition with LVM and swap
- Uses default credentials, which should be changed after installation
- Runs `sshd` on start

Before running you should replace the password in the line `<d-i passwd/user-password-crypted password mkpasswdoutput>` with a password generated using `mkpasswd` inside the preseed file.

### Steps Before Running the Playbook

1. **Generate a Public Key Pair**: Put the public key in the `public_keys/` directory in the root of the repository. Only the public key is needed as `.pub`.
2. **Customize Variables**: Customize `group_vars/all` as needed.
3. **Create A .env File**: In the `badgehub` directory, run `cp .env.default .env` and then customize the `.env` file as needed.
4. **Generate a `hosts.ini` File**: Follow the example to include host information.

### Running the Playbook

You can start the playbook with:

```sh
ansible-playbook -i hosts.ini -k playbook.yaml --ask-become-pass
```

## License

MIT
