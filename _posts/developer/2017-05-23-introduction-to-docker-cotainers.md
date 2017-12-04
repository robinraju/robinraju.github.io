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

***Docker is revolutionizing IT!*** we've been hearing this for a while. My search for what exactly is docker and what it 
can do for me landed me on the their official [documentation](https://docs.docker.com/). 

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

Docker Engine provides the core capabilities of managing containers. It interfaces with the underlying Linux operating system to expose simple APIs to deal with the lifecycle of containers.

Docker Tools are a set of command-line tools that talk to the API exposed by the Docker Engine. They are used to run the containers, create new images, configure storage and networks, and perform many more operations that impact the lifecycle of a container.

Docker Registry is the place where container images are stored. Each image can have multiple versions identified through unique tags. Users pull existing images from the registry and push new images to it. [Docker Hub](https://hub.docker.com/) is a hosted registry managed by [Docker, Inc](https://www.docker.com/). It’s also possible to run a registry within your own environments to keep the images closer to the engine.


### Docker vs Virtual Machines 
{% include figure image_path="/assets/images/posts/developer/docker/containers-vms-together.png" alt="Docker vs VM" caption="Docker vs VM" %}
Operating system (OS) virtualization has grown in popularity over the last decade as a means to enable software to run predictably and well when moved 
from one server environment to another. In a virtual machine, valuable resources are emulated for the guest OS and hypervisor, which makes it possible to run many 
instances of one or more operating systems in parallel on a single machine.

On the other hand, Containers provide a way to run these isolated systems on a single server/host OS. They sit on top of a physical server and its host OS, 
e.g. Linux or Windows. Each container shares the host OS kernel and, usually, the binaries and libraries, too. Shared components are read-only, 
with each container able to be written to through a unique mount. This makes containers exceptionally “light” – containers are only megabytes in size and 
take just seconds to start, versus minutes for a VM.


