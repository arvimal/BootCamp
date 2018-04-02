# Docker

**References**

1. `The Docker Book`, by Jim Turnbull
2. `KataCoda` - Docker Courses


## Introduction

* Containers are lean versions of an Operating System.
* They contain the bare minimum required to execute the process running on it.
* Containers bring their dependencies with them.
* Containers run using the Linux Kernel's CGroup and Namespace features.
* Containers can run on full-blown OSs, cloud servers (Azure, OpenShift, AWS)  etc.

## Docker, and how it works

* Docker consists of the `docker` daemon, and a client interface.
* The `docker` client interface provides several switches to work with the daemon.
* Docker uses images that can be pulled from either a private or a public registry.
* Containers (multiple ones) can be started off these images.
* Once the containers are done with, they can be stopped and deleted.
* Containers that are not deleted, can be started again.
* Docker is different from Virtual machines since it doesn't need a Hypervisor to run on. The kernel on the host machine is the one responsible to control these.
* The features such as CGroups, namespaces, and device-mapper in the Linux kernel makes Docker possible.
* Docker containers are light-weight since it stays off the Hypervisor altogether.
* Very fast compared to virtual machines, usually in sub-seconds.
* No need for installation of the entire OS (its already done in the form of 'images')


## Applications, in Docker

* Applications get bundled in a container image with all its dependencies.
* The container image is hosted on a Registry.
* `docker pull` or `docker run` gets the container image from the registry.
* The downloaded image is pulled and stored in `/var/lib/docker` on the local host.
* The Container when started from the Container image is a process which specific privileges, and is isolated from other processes on the host machine.

## Images and containers
* Images are the files which are created from an installation, which are stripped-down.
* Images are the source of your containers. 
* The containers are launced from images.
* Images can be stored in a registry.
* A container is the running instance of an image.

### NOTE:
* Containers are started from images, hence is analogous to processes spawned from a binary.
* Containers, even if stopped, are available to be started again. ie.. A once-started container need not be spawned from the image again. It will be present till it's deleted using `docker rm <container-name> [or container id]`.
* Docker images downloaded from any registries are stored in /var/lib/docker/devicemapper/devicemapper/data, which is an EXT4 filesystem.

## Layering
* Each image is made up of layers.
* The lowest layer is the root filesystem.
* Top layer is writable and created when a container is launched.
