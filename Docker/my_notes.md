### Concepts of containers ( Under the hood)

Containers are NOT lightweight virtual machines.
They are simply processes running on the host OS, but with special isolation and resource control mechanisms (like namespaces and cgroups) that make them behave like independent systems. They have a PID and Parent PID (PPID) just like other processes.

### Namespace: 
Namespaces are a Linux Kernel feature that makes it possible to limit what a process can perceive in/of the system. Essentially, namespaces create a controlled and isolated env for the processes giving them the illusion of isolated system resources. Think of it like invisible fences of your programs which keep your processes from seeing and messing with each other.

One of the overall goals of namespaces is to support the implementation of containers, a tool for lightweight virtualization (as well as other purposes) that provides a group of processes with the illusion that they are the only processes on the system.

Container engine uses several namepspaces including PID namespace , NET,IPC,MNT,UTC (isolation of host and domain names).
By default, all processes use a common namespace and group, which grants them broad access to the system. But when we create a container, Docker mocks a system view (creating a new context) and give the container its own principal user, network configuration, mount folders, filesystem, and so on. As a result, the container has its own pseudo-isolated environment with a mix of shared and unshared resources; which gives the impression that it’s a separate OS, but it’s using the same kernel.


### Cgroup (control group)
CGroups are another essential feauture of containers. They are again a Linux kernel feature that limits, accounts for and isolates resource usage (CPU, memory, disk I/O etc) for a group pf processes. You can think of them as traffic cops for your programs that make sure no one takes or uses too much resources. Cgroups enable sharing the available hardware resources with containers and enforce usage limits.

Container engines use the following cgroups:

Memory — Managing memory allocation and usage.
HugeTBL — Accounting usage of huge pages by a process group.
CPU — Managing CPU time and usage.
CPUSet — Binding a group of processes to specific CPUs.
BlkIO — Measuring and limiting the amount of I/O operations by group.
net_cls and net_prio — Classifying and assigning network priorities to the traffic for traffic control.
Devices — Managing read/write access devices.
Freezer — Freezing a group. Useful for checkpointing (saving the state of a process) and migration.

** Utilizing the Linux kernel features of Namespace and CG , containers run well within their boundaries ( scope and resource wise) unable to see and access what lies beyond their view.

### How does docker help??

Docker is just an orcehestration layer or a user friendly interface for creating and working with containers. Below is sequence of events that happen when we run docker command

Docker client -> Docker daemon (dockerd) -> Container daemon (containerd) -> runc

** Docker's best friends:
- Docker client - User-facing CLI
- Docker Deamon (dockerd) - Daemon responsible for managing containers and handling client requests — it is in charge of making sure containers do what you asked for.
- containerd - Industry-standard container runtime that helps manage containers — think of this like a special worker that actually does the work of generating and guaranteeing that the programs run inside containers.
- runc - Command-line tool for spawning and running containers — this is like a tool that starts and runs the programs inside containers.

So when you use Docker, you tell Docker what to do, Docker’s manager (dockerd) asks its worker (containerd) to do the job, and the worker uses special features and rules (namespaces, cgroups, and runc) to make sure the job gets done within an/the isolated environment (container).

One of the key players is containerd. Containerd utilizes the unshare() system call with flags such as CLONE_NEWNS, CLONE_NEWUTS, CLONE_NEWIPC, CLONE_NEWNET, and CLONE_NEWPID — see man page — to mock the system for the container runtime. This system call is at the core of the container's technology, enabling the creation of these pseudo-isolated environments where containers operate. Think of it as the magic spell that creates these special, separate spaces where containers can do their work without being able to interfere with processes running outside of it.

### Doker container workflow

1 - Write application code (python, nodeJs, Java etc)

2- Write Dockerfile to create docker image using dokcerfile instructions. THis is the blueprint of the container and defines base OS, runtime, app dependencies , app code and startup scripts (CMD or ENTRYPOINT)

3 - Build Image using docker build

4 - Run container using the image - docker run

5 - Store image in DockerHub - docker push

6 - Manage and monitor container using commands below.

`docker ps`          # Running containers

`docker logs`        # Logs

`docker exec -it`    # Access shell inside container

`docker stop`        # Stop container

`docker rm`          # Remove container

### What is DJango?

Django is a high-level Python web framework used to build web applications quickly, securely, and in a structured way. It provides a ready-made skeleton (framework) for building web applications, so developers don’t have to start from scratch.

Django follows the MVT (Model-View-Template) architecture.
It includes many built-in features like authentication, admin panel, ORM, and security protections.
Django versions must be compatible with the installed Python version.

### Basic setup flow

Install Python -> Install PIP -> Install DJango using pip install django

When you create a django project using command django-admin startproject project_name, it aauto creates pre-defined folder structure (skeleton) which includes project configuration file, setting,URL routing and application entry point.

Django provides a lightweight built-in development server: python manage.py runserver

### Docker persistent storage using volume and bind mounts

THis is a method of storing the data outside the containers using volume ,bind mounts and tmpfs mounts

- **Volume mounts**: Volumes are managed by docker deamon and docker CLI and they retain data even if the container using them is removed. The volume needs to be mounted on the container to access it. Directly accessing teh volume data is not supported. When we create a volume, it's stored within a directory on teh docker host.
Docker volumes can use external storage filesystem like NFS or SMB

- **Bind Mount**: When you use a bind mount, a file or directory on the host machine is mounted from the host into a container. By contrast, when you use a volume, a new directory is created within Docker's storage directory on the host machine.

Bind mounts are appropriate for the following types of use case:

- Sharing source code or build artifacts between a development environment on the Docker host and a container.

- When you want to create or generate files in a container and persist the files onto the host's filesystem.

- Sharing configuration files from the host machine to containers. This is how Docker provides DNS resolution to containers by default, by mounting /etc/resolv.conf from the host machine into each container.

`docker volume create` , `docker volume ls` , `docker volume prune`

`docker run --mount type=volume, src=myvol, dest=/data, ro`

`docker run -v myvol:/data:ro `

Both the above commands will do the same thing but --mount is preferred because it has more options than --volume or -v

### Docker Networking Concepts

Docker network allow the containers to talk to each other and the host systems AND the also the isolation between containers if needed. When containers are created, they are assigned an eth0 interface and put in the bridge network by default . Since teh host and the container are in 2 different subnet , there has to be a bridge network so that they can talk which is virtual network named docker0
If the host system has internet access, the containers can also access the internet. 

There are different types of network Drivers :-

 - **Bridge** - Default network. We can create custom bridged network and join this network to a conatiner to create isolation between teh 2 containers.Docker creates this custom network in a seperate subnet. The container in customer network can still talk to the host but teh common veth docker0 is broken so the containers are isolated.
 
 `docker network create custom-network` --> This created a custo bridged network with a different subnet than the defualt brdiged network
 
` docker run -d --name login --network=custom-network nginx:latest`
 
 - **Host** - The container in teh host network doesn't get an IP assigned and can be directly accessed using the host IP but this is not secure.
 
 `docker run -d --name host_demo --network=host nginx:latest`
 
 - **Overlay** - This network is primarily used during container orchestration when we have multiple cluster hosts and we want networking between containers running on those different hosts
 
 - **none** - Completely isolate the container from the host and other containers

### Limitations of Docker

1) Docker is a single deamon process which is dockerd and is a single point of failure. If the deamon goes down, entire applications goes down since all the containers are managed by dockerd. Podman Solves this.

2) Docker deamon runs as a root user which can be a security threat because it creates a direct pathway to complete host exploitation if compromised. By default, the Docker daemon (dockerd) requires root privileges to manipulate Linux kernel namespaces, control groups (cgroups), routing tables, and storage devices. Tu run docker commands, a normal user must be added to the "docker" group which is created during docker installation. The Docker installation process creates a dedicated docker group purely as a usability trade-off to eliminate the need for users to type sudo before every single Docker command. While convenient, adding a user to the docker group is the security equivalent of granting them passwordless root access via sudo. Any account belonging to the docker group can execute a command to spin up a container, map the host's entire root filesystem into it, and completely take over the machine

The command-line interface (docker) communicates with the background daemon through a Unix domain socket located at /var/run/docker.sock. By default, this socket file is strictly owned by the root user. To let standard users use Docker without typing sudo, the installation sets the group ownership of that socket to the docker group. Podman solves this. 

3) Resource constraints if you are running too many containers on a single host.

### How to secure containers?

1) Use distroless images (scratch) which is a very light weight image with just the runtime and minimal system libraries. Use multi-stage builds.

2) Create custom bridge networks for container isolation.

3) Use tools like Sync to scan the container images.

`docker exec -it container_name /bin/bash` --- To go inside the container.

`docker images` -- TO list all teh images.

`docker ps -a` -- To list all the stopped and running containers.

`docker images rmi $(docker image ls -q)` -- To remove all the images from teh system.

`docker system prune` -- To clean unused images and containers
