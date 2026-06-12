# Ansible Role: Docker

This Ansible role installs Docker on Ubuntu hosts and starts a simple Hello World container. The role is used in the lesson 06 CI/CD assignment to automatically deploy a Docker container on both an Azure VM and an ESXi VM.

## Purpose

The purpose of this role is to automatically install Docker, load a pre-built Docker image, and start a Hello World container on port `8080`.

The role also performs a check to verify that the container is reachable.

## Requirements

This role is intended for Ubuntu hosts.

Required:

* Ansible
* Ubuntu host
* SSH access to the target host
* `become: true`
* Ansible facts, because `ansible_distribution_release` is used
* The `community.docker` collection

Install the required collection with:

```bash
ansible-galaxy collection install community.docker
```

## Role Variables

The following variable is defined in `defaults/main.yml`:

```yaml
docker_users:
  - testuser
```

This variable can be used to define which user should be able to work with Docker. In the current version, Docker is mainly installed system-wide and the container is started by Ansible.

## What This Role Does

This role performs the following steps:

1. Installs required packages such as `ca-certificates`, `curl`, `python3`, and `python3-requests`.
2. Creates the Docker keyring directory.
3. Downloads the official Docker GPG key.
4. Adds the official Docker repository.
5. Installs Docker Engine and related components.
6. Starts and enables the Docker service.
7. Verifies that Docker is installed correctly.
8. Copies `hello-world.tar` to the target host.
9. Loads the Docker image.
10. Starts the Hello World container.
11. Checks if the container is reachable at `http://127.0.0.1:8080`.

## Expected Docker Image

This role expects a Docker image tar file to exist at:

```text
{{ playbook_dir }}/hello-world.tar
```

In the lesson 06 workflow, this image is built and saved before Ansible runs:

```bash
docker save hello-world:latest -o ansible/hello-world.tar
```

Ansible then copies this image to the target host and loads it into Docker.

## Example Playbook

Example usage with a local role name:

```yaml
- name: Install Docker and start Hello World container
  hosts: azure:esxi
  become: true
  gather_facts: true

  roles:
    - Docker
```

Example usage when the role is installed from Ansible Galaxy:

```yaml
- name: Install Docker and start Hello World container
  hosts: azure:esxi
  become: true
  gather_facts: true

  roles:
    - aramairapetian.docker
```

## Installation via Ansible Galaxy

After publishing the role to Ansible Galaxy, it can be installed with:

```bash
ansible-galaxy role install aramairapetian.docker
```

The published role information can be checked with:

```bash
ansible-galaxy role info aramairapetian.docker
```

## Usage in requirements.yaml

The role can be installed reproducibly through `requirements.yaml`:

```yaml
---
roles:
  - name: aramairapetian.docker

collections:
  - name: community.docker
  - name: ansible.posix
```

Install the requirements with:

```bash
ansible-galaxy install -r requirements.yaml
```

## Verification

After execution, the role verifies that:

* Docker is installed;
* the Docker service is running;
* the Hello World image is loaded;
* the container is started;
* the web page is reachable on port `8080`.

