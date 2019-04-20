## Docker

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


## Applications, in Containers

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
* Containers are started from images, hence is analogous to processes spawned from a binary.


## Layering
* Each image is made up of layers.
* The lowest layer is the root filesystem.
* Top layer is writable and created when a container is launched.

#### Introduction:

* Docker is a container which is self-sufficient for your application. This help to build an application-centric docker instance, and then ship it across using the registry.
* Docker is different from Virtual machines since it doesn't need a Hypervisor to run on. The kernel on the host machine is the one responsible to control these.
* The features such as CGroups, namespaces, and device-mapper in the Linux kernel makes Docker possible.
* Docker containers are light-weight since it stays off the Hypervisor altogether.
* Very fast compared to virtual machines, usually in sub-seconds.
* No need for installation of the entire OS (its already done in the form of 'images')


#### Differences between images and containers

* Images are the files which are created from an installation, which are stripped-down.
* Images are the source of your containers.
* The containers are launced from images.
* Images can be stored in a registry.
* A container is the running instance of an image.


#### Layers in an image:

* Each image is made up of layers.
* The lowest layer is the root filesystem.
* Top layer is writable and created when a container is launched.


##### NOTE:

* Containers are started from images, hence is analogous to processes spawned from a binary.
* Containers, even if stopped, are available to be started again. ie.. A once-started container need not be spawned from the image again. It will be present till it's deleted using `docker rm <container-name> [or container id]`.
* Docker images downloaded from any registries are stored in /var/lib/docker/devicemapper/devicemapper/data, which is an EXT4 filesystem.


#### Generic commands (Creation, starting, stopping, removing, inspecting)


1. To know the various supported docker commands:


# docker


2. To know the docker version, including client and server


# docker version


3. To search for a particular image (not container)


# docker search <pattern>
Ex:
# docker search fedora


4. To download/pull a fedora image from the mirror:


# docker pull <listed-pattern-in-search>

NOTE: The <listed-pattern-in-search> would usually be in a <parent>/<child> format. So if I have a fedora image in my name, it would be <vimal>/<fedora>. It downloads in multiple layers, as shown below:

# docker pull learn/tutorial

Pulling repository learn/tutorial from <https://index.docker.io/v1>
Pulling image 8dbd9e392a964056420e5d58ca5cc376ef18e2de93b5cc90e868a1bbc8318c1c (precise) from ubuntu
Pulling image b750fe79269d2ec9a3c593ef05b4332b1d1a02a62b4accb2c21d589ff2f5f2dc (12.10) from ubuntu
Pulling image 27cf784147099545 () from tutorial


5. You can think of containers as a process in a box. The box contains everything the process might need, so it has the filesystem, system libraries, shell and such, but by default none of these are running. You 'start' a container by running a process in it.



6. List the pulled images using:


# docker images


7. To start/run a docker image:


# docker run <docker-image> <commands>


* Make a container echo 'Hello world':


# docker run learn/tutorial [or image-id] echo "Hello World"


* To start a container in the background, use `-d`.


# docker run Technical Notes:Docker:httpd:2.4 --name my-httpd-server -d

**NOTE:**

* __A `docker run <image-name>` will pull the image from the upstream registry, if it doesn't exist locally.__
* If it exists locally, the container is created from the local image.



8. Depending on the container, it can be connected via various methods like a normal host allows.



* For example, an httpd server serving content can be connected via http.
* Containers running in background can be connected through `bash` if it allows.



9. To connect to a container running in the background, via bash


# docker exec -it <container-name> bash


10. To list only the running containers on the local machine:


# docker ps


11. To list the all the containers (running/stopped) on the local machine:


# docker ps -a


12. To list the container IDs of all the containers on the local machine, use:


# docker ps -a -q


13. To delete all stopped containers (deleting a running container will fail):


# docker rm <ID> [or container name listed under `NAMES`]

# docker rm $(docker ps -a -q)


14. To delete an image


# docker rmi <image-id>

**NOTE: **

* An image cannot be deleted, if a container has been spawned from it.
* The container may be either running or stopped.
* The container spawned from the image has to be deleted prior deleting the image.



15. To start a stopped container:


a) Get the container ID using:

# docker ps -a

b) Start the container using the ID:

# docker start <container-ID>


16. Starting a container will start it in the background. To attach to the container, use:


# docker attach <container-ID>


17. To stop a running container


# docker stop <container-name> [or container id]


18. For containers which has bash (or other shells built-in), we can access it using `-it` which stands for `interactive text` based programs such as bash and other shells.


# docker run --name my_httpd_server -it Technical Notes:Docker:httpd:2.4 /bin/bash

**NOTE:**

* The above command is for creating a new container from an image, and accessing the bash prompt.
* Here, `httpd:2.4` is the image name



19. To access an already running container's bash prompt (if it provides /bin/bash)


# docker attach <container-id>/<container-name>

# docker exec -it <running-container-name> /bin/bash

**NOTE:**

* `attach` will only spawn a single instance of the shell.
* It's much better to use the second method to get access to a shell.



20. To start a stopped container and access it's bash prompt:


# docker start <container-ID> ; docker exec -it <container-id> bash


21. To list the metadata, or get more in-depth information about the stopped/running container:


# docker inspect <container-ID>/<container-name>

**NOTE:**

* The `inspect` command is useful to fetch vital information such as IP addresses and other info.


# docker inspect <container-name> | grep -i ipaddr

~~~
$ sudo docker inspect my_CS50 | grep -i ipadd
"SecondaryIPAddresses": null,
"IPAddress": "172.17.0.2",
"IPAddress": "172.17.0.2",
~~~

# docker inspect <container-name> | less (Search for `Env` to know the root pass)


20. To forcefully stop a container, it can be killed since it's just a process from the OS perspective.


# docker kill <container-name>

**NOTE:**

* A signal can be sent to the container using `-s <SIGNAL>`
* The valid signals are:


SIGHUP  Terminate process   Terminate line hangup
SIGINT  Terminate process   Interrupt program
SIGQUIT Create core image   Quit program
SIGABRT Create core image   Abort program
SIGKILL Terminate process   Kill program
SIGTERM Terminate process   Software termination signal
SIGUSR1 Terminate process   User-defined signal 1
SIGUSR2 Terminate process   User-defined signal 2


21. Containers must be stopped before being removed. To stop all containers in one go:


# docker stop $(docker ps -aq)


22. To remove all containers in one go, after it has been stopped:


# docker rm $(docker ps -aq)


23. To restart a container process, use


# docker restart <container-name>/<container-id>


24. To check the logs of a container, use:


# docker logs <container-name>/<container-id>

#### Attaching Persistent Storage to the containers

Container storage is said to be ephemeral, meaning its contents are not preserved after the container is removed. Containerized applications are supposed to work on the assumption that they always start with empty storage, and this makes creating and destroying containers relatively inexpensive operations.

Ephemeral container storage is not sufficient for applications that need to keep data over restarts, like databases. To support such applications, the administrator can provide a container with persistent storage.

Previously in this course, container images were characterized as immutable and layered, meaning they are never changed, but composed of layers that add or override the contents of layers below.

A running container gets a new layer over its base container image, and this layer is the container storage. At first, this layer is the only read-write storage available for the container, and it is used to create working files, temporary files, and log files. Those files are considered volatile; an application will not stop working if they are lost. The container storage layer is exclusive to the running container, so if another container is created from the same base image, it will get another read-write layer.

![](./Docker-Notes_files/persistent-layer.png)


#### Setting up a directory on the local host to act as storage:


* Create a folder with owner and group root (notice the root prompt #):


# mkdir /var/dbfiles


* The container user must be capable of writing files on the folder. If the host machine does not have the container user, the permission should be defined with the numeric user ID (UID) from the container. In case of the mysql service provided by Red Hat, the UID is 27:


# chown -R 27:27 /var/dbfiles


* Allow containers (and also virtual machines) access to the folder:


# chcon -t svirt_sandbox_file_t /var/dbfiles


* Mount the volume onto the docker container


# docker run -v /var/dbfiles:/var/lib/mysql mysql # other options required by the MySQL image omitted

#### NOTE:

* __To bind mount a host folder to a container, add the -v option to the docker run command, specifying the host folder path and the container storage path, separated by a colon (:).__



* __To list the available selinux contexts, use:__



__# semanage fcontext -l__

__Hence, to list the selinux context for docker, grep for `svirt_sandbox` (don't use `docker`, it will list a lot of contexts)__

__# semanage fcontext -l | grep -i svirt_sandbox__

__Here, any data written to the location /var/lib/mysql in the container, will actually write the data to the location /var/dbfiles in the localhost.__

#### Sharing Storage among containers



#### Accessing Container Resources


* When a container is created, a default command is executed according to what is specified by the container image. However, it may be necessary to execute other commands to manage the running container.



* The docker exec command starts an additional process inside a running container:


# docker exec 7ed6e671a600 cat /etc/hostname
7ed6e671a600


* The previous example used the container ID to execute the command. It is also possible to use the container name:


# docker exec my-httpd-container cat /etc/hostname
7ed6e671a600


* Sometimes it is desirable to run a Bash shell inside the container. The docker exec command allows the execution of the shell:


# docker exec -it my-httpd-container /bin/bash
bash-4.2#


* Options -t and -i are usually needed for interactive text-based programs, so they get a proper terminal, but not for background daemons.


#### NOTES on exec:


* Use `exec` with `-it` to connect to an interactive terminal on `**already** **running`** containers.


# docker exec 7ed6e671a600 cat /etc/hostname
7ed6e671a600

The previous example used the container ID to execute the command. It is also possible to use the container name:

# docker exec my-httpd-container cat /etc/hostname
7ed6e671a600


* Use `run` and `-it` to get an interactive terminal on containers that are being spawned (run) by the current command.
* `exec` works only on running containers.. and not stopped/not-spawned ones.


# docker exec -it my-httpd-container /bin/bash
bash-4.2#

Options -t and -i are usually needed for interactive text-based programs, so they get a proper terminal, but not for background daemons.

#### Mapping network ports

Accessing the container from the external world could be a challenge. It is not possible to specify the IP address for the container that will be created, and the IP address changes for every new container. Another problem is that the container network is only accessible by the container host.

To solve these problems, it is possible to use the container host network model combined with network address translation (NAT) rules to allow the external access. To achieve this, the -p option should be used:

# docker run -d --name httpd -p 8080:80 do276/httpd

In the previous example, requests received by the container host on port 8080 from any IP address will be forwarded to port 80 in the container.

It is also possible to determine a specific IP address for the port forward:

# docker run -d --name httpd -p 192.168.1.5:8080:80 do276/httpd

If a port is not specified for the host port, the container will pick a random available port:

# docker run -d --name httpd -p 192.168.1.5::80 do276/httpd

Finally, it is possible to listen on all interfaces and have an available port picked automatically:

# docker run -d --name httpd -p 80 do276/httpd

### Chapter 3. Managing Images

By default, docker search and pulls from the Docker hub at `hub.docker.io`.

#### Adding additional registries

To configure extra registries for the docker daemon, the /etc/sysconfig/docker must be updated. For a RHEL host, an extra parameter must be added to the file:

ADD_REGISTRY='--add-registry registry.access.redhat.com --add-registry1 servera.lab.example.com:50002'


* The --add-registry parameter requires the registry FQDN host and port.
* The FQDN host and port number where the docker-registry service is running.


**NOTE:**

* The docker daemon requires a full restart to make the changes valid.


To access a registry, a secure connection is needed with a certificate. For a closed environment where only known hosts are allowed, the /etc/sysconfig/docker may be customized to support insecure connections for a RHEL host:

INSECURE_REGISTRY='--insecure-registry1 servera.lab.example.com:50002'


* The --insecure-registry parameter requires the registry FQDN host and port.
* The FQDN host and port number where the docker-registry service is running.


#### Frequent commands


1. Search for docker images


# docker search [OPTIONS] <term>
# docker search fedora


2. Pull images (either searched or known)


# docker pull [OPTIONS] NAME[:TAG] | [REGISTRY_HOST[:REGISTRY_PORT]/]NAME[:TAG]
# docker pull fedora:24

**NOTE:**

* The `pull` command takes in a registry.
* If no registry is provided, the first registry listed at the /etc/sysconfig/docker configuration file from the `ADD_REGISTRY` line will be used.



3. List downloaded/locally-cached images


# docker images
REPOSITORY       TAG    IMAGE ID     CREATED     VIRTUAL SIZE
docker.io/httpd  latest 1c0076966428 4 weeks ago 193.4 MB

**NOTE:**

* The image files will be stored at the /var/lib/docker directory from the docker daemon's host if the default configuration is used.
* On the other hand, if a LVM thin storage is used to store images, the LVM volume mount point will be used instead.



4. Pull an image based on tags


# docker pull Technical Notes:Docker:mysql:5.5

**NOTE:**

* An image tag is a mechanism from the docker-registry service to support multiple releases from the same project.
* This facility is useful when multiple versions from the same software are provided, such as a production-ready container or the latest bits from the same software developed for the community evaluation.
* Any operation where a container image is requested from a registry accepts a tag parameter to differentiate among multiple tags.
* If no tag is provided, the one called latest will be used.
* For instance, to pull an image with the tag 5.5 from mysql, the above command can be used.



5. Start a container based on a tag, provided multiple tags exist either locally or remotely for the same image.


# docker run Technical Notes:Docker:mysql:5.5

#### Transferring Images to and fro

Suppose a developer finished testing a custom container in his machine and needs to transfer this container image to another host, for another developer to use it, or to a production server. There are three ways this could be accomplished:


1. Export the running or stopped container to a tar file.
2. Save the container image to a tar file.
3. Publish (push) the container image to an image registry.


##### Exporting and importing containers


* All containers are created from an immutable container image, but when started they get an additional layer where changes can be made that override contents of the original image.
* The running container sees this read-write layer, plus the original read-only image, as a single filesystem.
* This filesystem can be exported to a tar file using the docker export command.



1. To export a running or stopped container:


# docker export [-o FILE_NAME] CONTAINER_NAME_OR_ID

**NOTE:**

* If the -o option is not used the tar contents are sent to the standard output as binary data.


For example, to export the container named myawesomeapp to file myapp.tar the command would be:

# docker export -o myapp.tar myawesomeapp

**IMPORTANT**__:__

* __The output of the docker export command is NOT a container image file. It has no layers and no metadata.__
* __Any tar file can be imported to the Docker cache as a container image by using the `docker import` command.__



2. To import a tar file which contains a container, use:


# docker import FILE_NAME [IMAGE_NAME[:TAG]]

For example, to import file myapp.tar to a container image named myawesomeapp the command would be:

# docker import myapp.tar myawesomeapp

__After the tar file was imported to a container image, this image can be used to create new containers the same way as images pulled from a registry. There is no distinction after the image is in the Docker cache.__

##### Saving and loading images

An existing image from the Docker cache can be saved to a tar file using the docker save command.

**NOTE:**

* An image saved from the docker local cache, **IS** a docker image and contains the proper metadata and original layers.
* An image cannot be directly saved from the registry without first pulling it locally.
* A file created by the `save` command can be used for backups, and can be loaded to the local docker cache.



1. To save an image from the local docker cache.


# docker images # To find the images you're interested in.

# docker save -o <file-name-to-save-the-image-as> <local-image-ID>

Ex:
 $ sudo docker images
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
docker.io/fedora                                     latest              11a5107645d4        11 weeks ago        204.4 MB
registry.access.redhat.com/rhceph/rhceph-1.3-rhel7   latest              8d6844d4fb9d        8 months ago        354.2 MB

$ sudo docker save -o my_fedora 11a5107645d4

**NOTE:**

* The `-o` switch has to be used, else the image content would be printed to stdout which would appear gibberish.



2. An image file that is saved, can be loaded to the docker cache using:


# docker load -i <file-name>

Ex:
# docker load -i mysql.tar

**NOTE:**

* To save disk space, the file generated by the save verb can be gzipped.
* The `load` verb will automatically gunzip the file before importing it to the daemon's cache directory.


##### Save versus export

The previous headings showed both docker export/import and docker save/load allows to have a custom container image saved to a tar file that can be transferred to a different host and used there to create new containers. When to use one of the other approach?

Using docker save/load is the preferred way. It has a few advantages:


* All image metadata is preserved.
* Layers are preserved and can be shared among different images, preserving container ephemeral storage disk space.


But docker export/import has its uses:

* Easy way to preserve a container filesystem state for post-mortem analysis.
* Images with fewer layers may be lightier to run, specially when little sharing of layers is expected.


When unsure if an image in the Docker cache was put there by load or export, the docker history command is invaluable. It shows all image layers in reverse order (as a stack). Among the layers are the image metadata which will be presented on the next chapter, when Dockerfiles are presented.

As an example, the following command shows the layers and metadata for the nginx image from the Docker Hub when this book was written:

# docker history nginx
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
6e36f46089ed        39 hours ago        /bin/sh -c #(nop) CMD ["nginx" "-g" "daemon o   0 B
8d66bf39903a        39 hours ago        /bin/sh -c #(nop) EXPOSE 443/tcp 80/tcp         0 B
556c463c2460        39 hours ago        /bin/sh -c ln -sf /dev/stdout /var/log/nginx/   0 B
5068080f8d28        39 hours ago        /bin/sh -c apt-key adv --keyserver <hkp://pgp>.   9.484 MB
7608c10ea730        39 hours ago        /bin/sh -c #(nop) ENV NGINX_VERSION=1.9.11-1~   0 B
d4e3870153bf        39 hours ago        /bin/sh -c #(nop) MAINTAINER NGINX Docker Mai   0 B
a0e9fe2f8803        42 hours ago        /bin/sh -c #(nop) CMD ["/bin/bash"]             0 B
1e58eecba27a        42 hours ago        /bin/sh -c #(nop) ADD file:6e3677c176d6d774f0   125.1 MB

In the previous output, entries containing (nop) are metadata. All other entries show changes introduced by a layer compared to the previous layers.

The docker history command is also a way to check what is inside a container image without having to create a container from it.

##### Publishing an image to a registry
A container image may be published to Docker Hub for a wider distribution; it requires an account and, depending on the container's characteristics, a subscription will be needed. To access it using the docker command line, the user must log into the Docker Hub with the login verb.

# docker login

To push an image to the registry, it must be stored in the docker's cache, and it should be tagged for identification purposes. To tag an image, the tag verb is provided:

# docker tag IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]

For instance, to tag the nginx image for deployment to the Docker Hub private account called myuserName with the latest tag, the following command can be executed:

# docker tag nginx myuserName/nginx

Finally, to push the image to the registry, run the following command:

# docker push myuserName/nginx

Alternatively, an image may be pushed to a registry for a wider distribution via a private registry. Likewise, the push process is identical to the Docker Hub, but the registry address must be passed as a parameter for the push verb. Therefore, the syntax requires the FQDN from the registry and the port number:

# docker push myuserName/nginx servera.lab.example.com:5000/nginx

##### Deleting an image

Any image downloaded to the Docker cache will be kept there even if no containers are using it. However, an image may become outdated, and it should be subsequently replaced.


