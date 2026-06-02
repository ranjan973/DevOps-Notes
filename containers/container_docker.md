# Container concepts

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
