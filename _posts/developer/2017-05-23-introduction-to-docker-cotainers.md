---
layout: single
title: An introduction to docker containers
read_time: true
category: developer
tags: [DevOps, Docker, Linux, Open Source, Technology]
header:
    teaser: /assets/images/posts/developer/docker-logo.png
comments: true
author_profile: false
share: true
related: true
permalink: /:categories/:year-:month-:day-:title/
excerpt: Docker is a tool that enables you to create, deploy, and manage lightweight, stand-alone packages that contain 
         everything needed to run an application (code, libraries, runtime, system settings, and dependencies). 
         These packages are called containers.
published: true
---

***Docker is revolutionizing IT!*** I've been hearing this for a while. My search for what exactly is docker and what it 
can do for me landed me on the their official [documentation](https://docs.docker.com/){:target="_blank"}. 

> **Docker** is a tool that enables you to create, deploy, and manage lightweight, stand-alone packages that contain 
everything needed to run an application in a loosely isolated environment (code, libraries, runtime, system settings, and dependencies). 
These packages are called containers.
{: .notice--success}

![image-right](/assets/images/posts/developer/docker/docker-container.png){: .align-right}
Docker containers are lightweight, as they run on a single machine and share its kernel. It isolate applications from one another and from the underlying infrastructure.
They start instantly and use less RAM with minimized disk usage. With Docker, one can manage their infrastructure in the same way they manage their applications.

Docker has three essential components:
- Docker Engine
- Docker Tools
- Docker Registry

**Docker Engine** provides the core capabilities of managing containers. It interfaces with the underlying Linux operating system to expose simple APIs to deal with the lifecycle of containers.

**Docker Tools** are a set of command-line tools that talk to the API exposed by the Docker Engine. They are used to run the containers, create new images, configure storage and networks, and perform many more operations that impact the lifecycle of a container.

**Docker Registry** is the place where container images are stored. Each image can have multiple versions identified through unique tags. Users pull existing images from the registry and push new images to it. [Docker Hub](https://hub.docker.com/) is a hosted registry managed by [Docker, Inc](https://www.docker.com/). It’s also possible to run a registry within your own environments to keep the images closer to the engine.


## Docker vs Virtual Machines 
{% include figure image_path="/assets/images/posts/developer/docker/containers-vms-together.png" alt="Docker vs VM" caption="Docker vs VM" %}
Operating system (OS) virtualization has grown in popularity over the last decade as a means to enable software to run predictably and well when moved 
from one server environment to another. In a virtual machine, valuable resources are emulated for the guest OS and hypervisor, which makes it possible to run many 
instances of one or more operating systems in parallel on a single machine.

On the other hand, Containers provide a way to run these isolated systems on a single server/host OS. They sit on top of a physical server and its host OS, 
e.g. Linux or Windows. Each container shares the host OS kernel and, usually, the binaries and libraries, too. Shared components are read-only, 
with each container able to be written to through a unique mount. This makes containers exceptionally “light” – containers are only megabytes in size and 
take just seconds to start, versus minutes for a VM.

## Installing Docker

Docker is available in two editions: **Community Edition (CE)** and **Enterprise Edition (EE)**.
Docker community edition is suitable for developers and small teams who are looking to get started with
running their apps on docker. Here I am going to explain how to install it on Ubuntu.

Please find the below links to install docker on Mac and Windows
- [Docker for Mac](https://docs.docker.com/docker-for-mac/install/#download-docker-for-mac){:target="_blank"}
- [Docker for windows](https://docs.docker.com/docker-for-windows/install/){:target="_blank"}

1. Update the apt package index:
```sh
$ sudo apt-get update
```
2. Install packages to allow apt to use a repository over HTTPS:
```sh
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```
3. Add Docker’s official GPG key and verify:
```sh
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo apt-key fingerprint 0EBFCD88
```
4. Setup the stable repository
```sh
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
5. Install Docker-CE
```sh
$ sudo apt-get update
$ sudo apt-get install docker-ce
```
6. Verify that Docker CE is installed correctly by running:
```sh
$ docker version
```

## Launching docker containers
Docker containers are launched from existing docker images which are stored in a registry. 
One can choose from a  private or public registriy for pulling images. Only authenticated users can use a private registry. 
Images from a public registry can be accessed by anyone. [Docker Hub](hub.docker.com){:target="_blank"} is such a public registry.

Lets run Nginx web server using docker

1. Pull the NGINX docker image to local machine.
```sh
$ docker pull nginx
```
2. Run docker container from image
```sh
$ docker run -p 8080:80 --name web -d nginx
```
You may have noticed the additional options passed to `docker run` command.
- **-p** This tells Docker Engine to expose the container's port `80` on the host's port `8080`.
- **- -name** This will assign a name to our running container. In our case container will be named as `web`. If we omit this part, Docker engine will assign a random name.
- **-d** This option will instruct Docker Engine to run in detached mode (background). Otherwise the container will run in foreground, blocking access to the shell.
To verify our container is running in background, try this command.
```sh
$ docker ps
```
It will output the following.
```sh
CONTAINER ID  IMAGE COMMAND                CREATED         STATUS         PORTS                 NAMES
b9ced16aec85  nginx "nginx -g 'daemon ..." 17 minutes ago  Up 17 minutes  0.0.0.0:8080->80/tcp  web
```
to stop and remove the container use the following commands.
```sh
$ docker stop web
$ docker rm web
```

### Adding Storage to containers

Any thing stored within a container will be lost when the container is terminated. To persist the data beyond the life of a container, 
we need to attach a volume to it. Volumes are directories from the host file system.

```sh
Create a directory on the host machine:
$ mkdir nginx-html
```
Now, let's launch the container by mounting the `nginx-html` directory.
```sh
$ docker run -p 8080:80 --name web -d -v $PWD/nginx-html:/usr/share/nginx/html nginx
``` 
The `-v` option points the `nginx-html` directory from the host file system to the container's directory. 
Any change made to this directory will be visible at both the locations.
Now add any .html files inside this newly created directory and we can see it being served by nginx at `http:localhost:8080`

### Accessing shell inside container
We can get into a running container and perform any supported commands there by running the following command:
```sh
$ docker exec -it web /bin/bash
```
This will give access to the bash shell inside our nginx container.
```sh
root@b9ced16aec85:/# 

```
### Copying files to a running container
We can copy files from the host machine to a running container as follows:
`docker cp /pathto/file <container-name>:/path/`
```sh
$ docker cp test.html web:/usr/share/nginx/html
```
### Few more basic docker commands

```sh
Monitor the logs inside the container
$ docker container logs <contaner name> -f

List all containers
$ docker container ls
  	[options]	
  		-a  all
  		-q  not running
  		
Stop a running container
$ docker container stop <container name>

Start a stopped container
$ docker container start <container name>

Remove a container
$ docker container rm <name>  		
```
### Learn More
You can learn more on Docker & Containers using Interactive Browser-Based Scenarios. [Find it here](https://www.katacoda.com/courses/docker){:target="_blank"}

### Conclusion
Its hard to keep track of dependencies without proper tools. Docker is such a versatile tool that helps us to build, package and deploy
applications along with its dependencies. I hope this post can help one to get started with docker.