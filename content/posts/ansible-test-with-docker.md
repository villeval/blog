---
title: "Testing your Ansible playbooks with Docker"
date: 2019-08-07T00:00:00+02:00
description: "How to utilize Docker to test Ansible-playbooks."
author: "Ville Valtonen"
draft: false

---

When I was writing my first Ansible playbooks from scratch, for a real customer instead of training, I wasn't too confident to start testing them against real cloud instances or servers. I wasn't too enthusiasted about setting up a virtual machine with Hyper-V (Hyper-V blocks use of VirtualBox and Docker for Windows requires Hyper-V enabled) either or configuring Vagrant, especially not for Windows, which is currently the main operating system I use. That's when Docker came into the picture. I was already very familiar with Docker and I thought it would be great for testing my playbooks, since containers provides very isolated environment (unless specifically given) so I would not mess up with my host machine or any real instance. Docker containers are also really fast to spin up from an image, so I could rerun the playbook from scratch over and over again and get very quick feedback about my playbook. Since then, I've used it frequently while creating my initial versions of playbooks and while training or exploring some new features and modules of Ansible. Without going further about the background of this project, let's get into the actual testing!

### Requirements
In fact, there aren't that many requirements. Basically all you need is Ansible and Docker installed on your chosen machine and some text editor.

### Folder structure
The folder structure of this project is fairly simple. In your chosen directory, create two folders called "ansible" and "docker". Few folders will be created under "ansible"-folder for specific Ansible related files, but all other files will be placed under project root or "docker"-directory.

### Creating Dockerfile
At first, we need a Dockerfile, which is the blueprint for our test container. As a base image, we use CentOS (version 7), which is a popular Linux distribution for different kind of servers and instances. To be able to use SSH-connection for Ansible playbook execution, we have to install OpenSSH-server and do couple of configurations related to it including enabling root-user login without password to the container. The following code should be created into "docker"-folder with name Dockerfile.

{{< highlight dockerfile >}}
FROM centos:centos7

# installing openssh-server
RUN yum -y update; yum clean all
RUN yum install -y openssh-server sudo
RUN sed -i 's/#PermitRootLogin yes/PermitRootLogin without-password/' /etc/ssh/sshd_config

# creating necessary folder for ssh service
RUN mkdir /var/run/sshd

# creating key
RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -N ''

# setting entrypoint for image
ENTRYPOINT ["/usr/sbin/sshd", "-D"]
{{< /highlight >}}

### Creating Ansible playbook
Now that we have a place to do things with Ansible playbook, it's time to create one. At first, we have to create inventory-file to specify the details of Ansible-host. Create the following file under "ansible/environments/localtest".

{{< highlight yaml >}}
[local]
ansible-test ansible_connection=docker ansible_host=ansible-test-centos
{{< /highlight >}}

Next we can define what we want to do with our playbook. This could be done directly with tasks too, but for this project we are using roles, which are great way to classify tasks and related things like variables, under different roles. The path for the following YAML-file should be "ansible/roles/helloworld/tasks"

{{< highlight yaml >}}
---
# echo hello world for testing

- name: printing hello world
  shell: echo "Hello from helloworld-role!"
{{< /highlight >}}

After specifying on low level what and where our playbook should do, the last thing is to create a playbook that combines all of this information. The following YAML-file is the playbook itself and it contains the hosts, tells to run tasks as root and which roles we want to run. Place the following file under "ansible"-directory.

{{< highlight yaml >}}
---
  - hosts: local
    become: true
    roles:
      - helloworld
{{< /highlight >}}

### Bringing it all together
Now that we have created the Ansible playbook and an Ansible-host which will be used as a target host, the last thing to do is combine the building and running of the container and execution of the playbook. For that, I created a simple Bash-script, which will build and run the container, execute the playbook and stop and clean up the container afterwards. The script is expecting one argument, which is used to provide a port number, in which the SSH port (22) of the container will be exposed on host machine. Disclaimer: during the first run, the script takes a longer time since it runs some updates and installations inside the container. After the first run, the container will run in no-time, thanks to cache of Docker.

{{< highlight bash >}}
#!/bin/bash

# initializing variables
EXPOSED_PORT=$1
DOCKER_CONTAINER_NAME="ansible-test-centos"
DOCKER_IMAGE_NAME="mycentos-ssh"

# building and running the docker image
docker build -t $DOCKER_IMAGE_NAME docker/
docker run -ti --name $DOCKER_CONTAINER_NAME -d -p $EXPOSED_PORT:22  $DOCKER_IMAGE_NAME

# running ansible playbook
ansible-playbook -i ansible/environments/localtest/ ansible/myplaybook.yml

# stopping and removing the container
docker stop $DOCKER_CONTAINER_NAME && docker rm $DOCKER_CONTAINER_NAME
{{< /highlight >}}

### Conclusion
In this post, I've shown you a quick way to test your Ansible playbooks locally without worrying about setting up something else than a simple container. I hope this guide helps you like it has been helping me during my endeavours with Ansible.