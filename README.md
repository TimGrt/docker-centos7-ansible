# CentOS 7 Ansible Test Image

CentOS 7 Docker container for Ansible playbook and role testing.  
This container is used to test Ansible roles and playbooks (e.g. with molecule) running locally inside the container.  
A non-priviledged user `ansible` is created with password-less sudo configured.

![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/timgrt/centos7-ansible) ![Docker Pulls](https://img.shields.io/docker/pulls/timgrt/centos7-ansible) ![CodeFactor Grade](https://img.shields.io/codefactor/grade/github/timgrt/docker-centos7-ansible/main)

## Tags

The following tags are available:

  - `latest`: Latest stable version of Ansible, on Python 2.7.x.

## How to Build

This image is built on Docker Hub automatically any time the upstream OS container is rebuilt, and any time a commit is made or merged to the `master` branch. But if you need to build the image on your own locally, do the following:

  1. [Install Docker](https://docs.docker.com/engine/installation/).
  2. Clone the repository and `cd` into this directory.
  3. Run `docker build -t centos7-ansible .`

## How to Use Standalone

  1. [Install Docker](https://docs.docker.com/engine/installation/).
  2. Pull this image from Docker Hub or use the image you built earlier, e.g. called `centos7-ansible:latest` for the next step.
  ```bash
  docker pull timgrt/centos7-ansible:latest
  ```
  3. Run a container from the image. To test my Ansible roles, I add in a volume mounted from the current working directory with ``--volume=`pwd`:/etc/ansible/roles/role_under_test:ro``).
  ```bash
  docker run --detach --privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro timgrt/centos7-ansible:latest
  ``` 
  4. Use Ansible inside the container.
  ```bash
  docker exec --tty [container_id] env TERM=xterm ansible --version
  ```
  ```bash
  docker exec --tty [container_id] env TERM=xterm ansible-playbook /path/to/ansible/playbook.yml
  ```

## How to Use with Molecule

  1. [Install Docker](https://docs.docker.com/engine/installation/).
  2. [Install Molecule](https://molecule.readthedocs.io/en/latest/installation.html).
  3. Add Image in molecule.yml.

For example:
```yaml
---
driver:
  name: docker
platforms:
  - name: centos7
    image: timgrt/centos7-ansible:latest
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    privileged: true
    command: "/usr/sbin/init"
    pre_build_image: true
provisioner:
  name: ansible
  config_options:
    defaults:
      interpreter_python: auto_silent
      callback_whitelist: profile_tasks, timer, yaml
      stdout_callback: yaml
    ssh_connection:
      pipelining: false
  inventory:
    host_vars:
      centos7:
        ansible_user: ansible
```

## Author

Created 2021 by Tim Grützmacher, inspired by [Jeff Geerling](https://www.jeffgeerling.com/)
