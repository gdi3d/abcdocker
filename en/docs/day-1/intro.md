# Introduction

Very often, the terms *Docker* and *containers* are used interchangeably, and while they are closely related, they are not the same thing.

**Docker** is a company that developed several products focused on containers, while **containers** are a technology based on Linux [cgroups](https://en.wikipedia.org/wiki/Cgroups).

!> If you want to learn more about cgroups, I recommend this article series (4 parts):  
https://www.redhat.com/sysadmin/cgroups-part-one  
https://www.redhat.com/sysadmin/cgroups-part-two  
https://www.redhat.com/sysadmin/cgroups-part-three  
https://www.redhat.com/sysadmin/cgroups-part-four

**Docker** played a major role in popularizing container technology among developers.

Containers are a way to package software and run it on different platforms (Windows, Linux, macOS, etc.) without worrying about compatibility or dependencies.

The only requirement is that the *host* where you want to run the software has a container tool installed.

There are different options for running containers:

- [Docker](https://www.docker.com/products/personal/)
- [Podman](https://podman.io/)
- [Containerd](https://containerd.io/)

One important thing to understand is that inside your container **only one process should run**. This means you should not use a single container to run your backend + database + static file server.

In that case, you would have 3 different containers, each running the appropriate process.

# Virtual Machines vs Containers

![vm vs docker](../images/vm-vs-docker.png)

Containers provide process isolation on a shared operating system, rather than operating system isolation on shared hardware, as is the case with virtual machines.

Containers do not use a hypervisor to run. Access to host resources is controlled by the container engine that is installed (Docker, Podman, Containerd, etc.).

# What are they useful for?

Containers allow you to make maximum use of a server’s resources, since you can run many applications, each in its own environment.

They also simplify operations because you don’t have to worry about dependency conflicts, updates, or the operating system an application requires.

Imagine a scenario where, on the same server, you need to run applications developed in *Python*, *PHP*, *Python*, and *NodeJS* at the same time, and where each language requires different versions.

Maintaining such an environment becomes very complex. One possible solution would be to virtualize the server and run different virtual machines to achieve that independence. However, designing something balanced and maintainable over time would be very difficult.

# Containers and Microservices

Using containers makes it much easier to develop microservices, for the reasons explained above.

You can set up a development environment with backends written in different languages and versions without spending time configuring or maintaining dependencies.

!> Microservices, unlike a monolithic architecture, are an architectural approach whose main goal is to design simple and independent *services* that communicate with each other via APIs and perform only a single, specific task within your backend.  
![monolith-microservices](../images/monilitico-microservicios.png)

# Images

To distribute your container, you need to create an *image*. The image contains everything required for your code to run.

This image is composed of layers that can be reused by other containers and that together form the file system inside your container.

![image-layers](../images/image-layers.png)

Each command creates a layer that is linked to the previous one (A → B → C → D → ...).

> If you modify the last line of the Dockerfile and rebuild the image, only that layer (G) will change. But if you modify the first one, all subsequent layers will need to be rebuilt.

!> More information: https://opensource.com/article/21/8/container-image