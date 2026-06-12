# Ansible Role: Docker

This Ansible role installs Docker on a Linux host and deploys a simple Hello World container. The role is mainly designed for Ubuntu-based Linux systems.

It is used in a CI/CD workflow where a Docker image is built, saved as a `.tar` file, copied to the target Linux hosts, loaded into Docker, and started as a running container.

## Purpose

The purpose of this role is to automate the installation of Docker and the deployment of a containerized Hello World application on Linux hosts.

This role demonstrates how Ansible can be used for configuration management and application deployment on multiple Linux servers, such as an Azure Linux VM and an ESXi Linux VM.

## Requirements

This role is designed for Linux hosts, specifically Ubuntu-based systems.

Required:

* Ansible
* Linux target host
* Ubuntu-based operating system
* SSH access to the target host
* `become: true`
* The `community.docker` collection
* A pre-built Docker image saved as `hello-world.tar`

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

This variable can be used to define which users should be allowed to work with Docker.

## What This Role Does

This role performs the following tasks:

1. Installs required packages.
2. Creates the Docker keyring directory.
3. Downloads the official Docker GPG key.
4. Adds the official Docker APT repository.
5. Installs Docker Engine and related components.
6. Starts and enables the Docker service.
7. Verifies the Docker installation.
8. Copies the pre-built Docker image to the Linux host.
9. Loads the Docker image.
10. Starts the Hello World container.
11. Checks whether the container is reachable on port `8080`.

## Expected Docker Image

This role expects the Docker image tar file to be available at:

```text
{{ playbook_dir }}/hello-world.tar
```

In the CI/CD workflow, the image can be created with:

```bash
docker build -t hello-world:latest ./docker
docker save hello-world:latest -o ansible/hello-world.tar
```

Ansible then copies this image to the target Linux host and loads it into Docker.

## Example Playbook

Example usage with the role installed from Ansible Galaxy:

```yaml
- name: Install Docker and deploy Hello World container
  hosts: azure:esxi
  become: true
  gather_facts: true

  roles:
    - aramairapetian.docker
```

## Installation

Install the role from Ansible Galaxy with:

```bash
ansible-galaxy role install aramairapetian.docker
```

Check the role information with:

```bash
ansible-galaxy role info aramairapetian.docker
```

## Usage with requirements.yaml

The role can also be installed through a `requirements.yaml` file:

```yaml
---
roles:
  - name: aramairapetian.docker

collections:
  - name: community.docker
```

Install the requirements with:

```bash
ansible-galaxy install -r requirements.yaml
```

## Verification

After the role has run successfully:

* Docker is installed on the Linux host.
* The Docker service is running.
* The Hello World image is loaded.
* The Hello World container is running.
* The container is reachable at `http://127.0.0.1:8080`.

## License

 MIT


## Author Information

Ara Airapetian
