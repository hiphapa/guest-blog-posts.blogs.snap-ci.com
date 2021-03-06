---
layout: post
title:  "Operating System Containers vs. Application Containers"
date:   2015-05-01
author: Akshay Karle
---

Thanks to [Docker](https://www.docker.com/), containers have gained significant popularity lately among Developer and Ops communities alike. Many people simply want to use Docker because of its rising popularity, but without understanding if Docker is really what they need. There are many container technologies out there to choose from, but there is a general lack of knowledge about the subtle differences in these technologies and when to use what.

# The need for containers

Hypervisor based virtualization technologies have existed for a long time now. Since a hypervisor or full virtualization mechanism emulates the hardware, you can run any operating system on top of any other, Windows on Linux, or the other way around. Both the guest operating system and the host operating system run with their own kernel and the communication of the guest system with the actual hardware is done through an abstracted layer of the hypervisor.

![hypervisor based virtualization](/assets/images/screenshots/containers/hypervisor-based-virtualization.jpg){: .screenshot .big}

This approach usually provides a high level of isolation and security as all communication between the guest and host is through the hypervisor. This approach is also usually slower and incurs significant performance overhead due to the hardware emulation. In order to reduce this overhead, another level of virtualization called "operating system virtualization" or "container virtualization" was introduced which allows running multiple isolated [user space](https://en.wikipedia.org/wiki/User_space) instances on the same kernel.

# What are containers?

Containers are the products of operating system virtualization. They provide a lightweight virtual environment that groups and isolates a set of processes and resources such as memory, cpu, disk, etc., from the host and any other containers. The isolation guarantees that any processes inside the container cannot see any processes or resources outside the container.

![OS virtualization](/assets/images/screenshots/containers/os-virtualization.jpg){: .screenshot .big}

The difference between a container and a full-fledged VM is that all containers share the same kernel of the host system. This gives them the advantage of being very fast with almost [0 performance overhead](https://en.wikipedia.org/wiki/Operating-system-level_virtualization#Overhead) compared with VMs. They also utilize the different computing resources better because of the shared kernel. However, like everything else, sharing the kernel also has its set of shortcomings --

* Type of containers that can be installed on the host should work with the kernel of the host. Hence you cannot install a Windows container on a Linux host or vice-versa.
* Isolation and security -- the isolation between the host and the container is not as strong as hypervisor-based virtualization since all containers share the same kernel of the host and there have been cases in the past where [a process in the container has managed to escape into the kernel space of the host](https://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2014-9357).

# Common cases where containers can be used

As of now, I have noticed that containers are being used for two major uses - as a usual operating system or as an application packaging mechanism. There are also other cases like using [containers as routers](http://www.flockport.com/flockport-labs-use-lxc-containers-as-routers/) but I don't want to get into those in this blog.

I like to classify the containers into special types based on how they can be used although I will also point out that it is not a must to use a container technology for just that case and you may very well use it for other cases. I've classified them this way because I find certain technologies easier to use for certain cases. Based on the two uses I mentioned above I've classified containers as OS containers and app containers.

## OS containers

OS containers are virtual environments which share the kernel of the host operating system but provide user space isolation. For all practical purposes you can think of OS containers as VMs. You can install, configure and run different applications, libraries, etc., just as you would on any OS. Just as a VM, anything running inside a container can only see resources that have been assigned to that container.

OS containers are useful when you want to run a fleet of identical or different flavours of distros. Most of the times containers are created from templates or images which determine the structure and contents of the container. It thus allows you to create containers that have identical environments with the same package versions and configurations across all containers.

![same or different distros on a host](/assets/images/screenshots/containers/os-containers.jpg){: .screenshot .big}

Container technologies like LXC, OpenVZ, Linux VServer, BSD Jails and Solaris zones are all suitable for creating OS containers.

## App containers

While OS containers are designed to run multiple processes and services, app containers are designed to package and run a single service. Container technologies like Docker and Rocket are examples of app containers. So even though they share the same kernel of the host there are subtle differences make app containers different, which I would like to talk about using the example of Docker:

#### Run a single service as a container

When a Docker container is launched, it [runs a single process](https://docs.docker.com/reference/run/). This process is usually the one that runs your application when you create containers per application. This very different from the traditional OS containers where you have multiple services running on the same OS.

#### Layers of containers

![layers of containers](/assets/images/screenshots/containers/docker-layers.png){: .screenshot .big}

Any `RUN` commands you specify in the [Dockerfile](https://docs.docker.com/reference/builder/) creates a new [layer](https://docs.docker.com/terms/layer/) for the container. At the end when you run your container, Docker combines these layers and runs your containers. Layering helps Docker to reduce duplication and increases the re-use. This is very helpful when you want to create different containers for your components, as you can start with a base image that is common for all the components and then just add layers that are specific to your component. Layering also helps when you want to rollback your changes as you can simply switch to the old layers and there is almost no overhead involved in doing so.

#### Built on top of other container technologies

Until sometime ago, [Docker was built on top of LXC](http://www.infoq.com/news/2014/03/docker_0_9). If you look at the [Docker FAQ](https://docs.docker.com/faq/), they mention a number of points which point out the differences between LXC and Docker.

<br/>

The idea behind app containers is that you create different containers for each of the components in your application. This approach works especially well when you want to deploy a distributed, multi-component system using the microservices architecture. The development team gets the freedom to package their own applications as a single deployable container, the operations teams gets the freedom of deploying the container on the operating system of their choice as well as the ability to scale both horizontally and vertically the different applications. The end state is a system that has different applications and services each running as a container which then talk to each other using the APIs and protocols that each of them supports.

In order to explain what it means to run an app container using Docker, let's take a simple example of a three-tier architecture in web development which has a `PostgreSQL` data tier, a `Node.js` application tier and an `Nginx` as the load balancer tier.

In the simplest cases, using the traditional approach, one would put the database, the Node.js app and Nginx on the same machine.

![simplest 3-tier architecture](/assets/images/screenshots/containers/simplest-3-tier-architecture.jpg){: .screenshot .big}

Deploying this architecture as Docker containers would involve building a container image for each of the tiers. You then deploy these images independently, creating containers of varying sizes and capacity according to your needs.

![typical 3-tier architecture with Docker containers](/assets/images/screenshots/containers/3-tier-architecture-using-docker.jpg){: .screenshot .big}

# Summary

So in general when you want to package and distribute your application as components, app containers serve as a good resort. Whereas, if you just want an operating system in which you can install different libraries, languages, databases, etc., OS containers are better suited.

Stay tuned for my next article in which I will cover how on the current project - [Snap CI](https://snap-ci.com) - we ended up using OS containers like OpenVZ and LXC over the app containers like Docker.

![OS Containers vs. App Containers](/assets/images/screenshots/containers/os-vs-app-containers.jpg){: .screenshot .big}

