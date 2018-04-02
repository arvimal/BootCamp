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

* Docker was created by `Docker Inc`, released in 2013.
* Docker consists of the `docker` daemon, and a client interface.
* The `docker` client interface provides several switches to work with the daemon.
* Docker uses images that can be pulled from either a private or a public registry.
* Containers (multiple ones) can be started off these images.
* Once the containers are done with, they can be stopped and deleted.
* Containers that are not deleted, can be started again.

## Applications, in Docker

* Applications get bundled in a container image with all its dependencies.
* The container image is hosted on a Registry.
* `docker pull` or `docker run` gets the container image from the registry.
* The downloaded image is pulled and stored in `/var/lib/docker`