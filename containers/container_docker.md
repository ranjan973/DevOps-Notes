# Container Concepts

## Concepts of Containers (Under the Hood)

Containers are **NOT lightweight virtual machines**.

They are simply processes running on the host operating system, but with special isolation and resource control mechanisms (such as Linux namespaces and cgroups) that make them behave like independent systems.

Like any other process, containers have:

- PID (Process ID)
- PPID (Parent Process ID)

---

# Linux Namespaces

Namespaces are a Linux kernel feature that limits what a process can see or access on a system.

They create a controlled and isolated environment for processes, giving them the illusion that they have their own dedicated system resources.

Think of namespaces as invisible fences that prevent processes from seeing or interfering with each other.

One of the primary goals of namespaces is to support containers, providing lightweight virtualization where a group of processes behaves as if it is running on its own machine.

---

## Common Namespaces Used by Containers

### PID Namespace

Provides process isolation.

A container sees only its own processes.

---

### NET Namespace

Provides network isolation.

Each container gets:

- Its own network interfaces
- Routing table
- Firewall rules
- IP addresses

---

### IPC Namespace

Provides isolation for:

- Shared memory
- Message queues
- Semaphores

---

### MNT Namespace

Provides filesystem mount isolation.

A container sees its own filesystem structure.

---

### UTS Namespace

Provides hostname and domain name isolation.

Containers can have their own hostname independent of the host.

---

## How Docker Uses Namespaces

By default, all Linux processes share common namespaces.

When a container is created, Docker creates new namespace contexts and provides the container with:

- Its own process view
- Network stack
- Hostname
- Filesystem mounts
- IPC resources

This creates a pseudo-isolated environment that appears to be a separate operating system, even though the container is still sharing the host kernel.

---

# Control Groups (cgroups)

cgroups are another Linux kernel feature that:

- Limit resource consumption
- Track resource usage
- Isolate resource allocation

Think of cgroups as traffic cops that ensure no process consumes excessive resources.

They allow multiple containers to share the same host while enforcing limits.

---

## Resources Managed by cgroups

### Memory

Controls memory allocation and usage.

---

### HugeTLB

Tracks huge page allocation and usage.

---

### CPU

Controls CPU scheduling and usage.

---

### CPUSet

Pins processes to specific CPU cores.

---

### BlkIO

Controls and limits disk I/O operations.

---

### net_cls and net_prio

Provides:

- Traffic classification
- Network prioritization

---

### Devices

Controls access to devices.

Examples:

- USB
- Storage devices
- GPU devices

---

### Freezer

Pauses process groups.

Useful for:

- Checkpointing
- Process migration

---

## Namespace + cgroups

Containers rely on:

```text
Namespaces → Isolation
cgroups    → Resource Control
```

Together they create safe boundaries for containers.

---

# How Does Docker Help?

Docker is a user-friendly platform that simplifies container creation and management.

Instead of manually dealing with namespaces and cgroups, Docker automates the entire process.

---

## Docker Execution Flow

```text
Docker Client
      ↓
Docker Daemon (dockerd)
      ↓
containerd
      ↓
runc
      ↓
Container
```

---

# Docker Components

## Docker Client

The command-line interface used by users.

Examples:

```bash
docker run
docker ps
docker images
```

---

## Docker Daemon (dockerd)

Background process responsible for:

- Managing containers
- Managing images
- Managing networks
- Handling client requests

It acts as the central manager.

---

## containerd

Industry-standard container runtime.

Responsibilities:

- Container lifecycle management
- Image management
- Execution coordination

Think of containerd as the worker that performs the actual container operations.

---

## runc

Low-level container runtime.

Responsibilities:

- Create containers
- Start containers
- Execute container processes

---

# How Containers Are Created

When you run:

```bash
docker run nginx
```

The sequence is:

```text
User
 ↓
Docker Client
 ↓
dockerd
 ↓
containerd
 ↓
runc
 ↓
Linux Namespaces + cgroups
 ↓
Container Process
```

---

## The Magic Behind Isolation

Container runtimes use the Linux `unshare()` system call.

Typical namespace flags include:

```text
CLONE_NEWNS
CLONE_NEWUTS
CLONE_NEWIPC
CLONE_NEWNET
CLONE_NEWPID
```

This creates isolated environments where container processes behave as if they are running on their own machine.

---

# Docker Container Workflow

## Step 1: Write Application Code

Examples:

- Python
- Java
- NodeJS
- Go
- PHP

---

## Step 2: Write Dockerfile

A Dockerfile acts as the blueprint for the container.

It defines:

- Base image
- Runtime
- Dependencies
- Application code
- Startup command

Example:

```dockerfile
FROM python:3.12

COPY . /app

WORKDIR /app

RUN pip install -r requirements.txt

CMD ["python","app.py"]
```

---

## Step 3: Build Docker Image

```bash
docker build -t myapp .
```

---

## Step 4: Run Container

```bash
docker run myapp
```

---

## Step 5: Push Image to Registry

```bash
docker push myrepo/myapp
```

Common registries:

- Docker Hub
- AWS ECR
- Azure ACR
- GitHub Container Registry

---

## Step 6: Monitor and Manage

### Running Containers

```bash
docker ps
```

---

### View Logs

```bash
docker logs <container>
```

---

### Open Shell

```bash
docker exec -it <container> /bin/bash
```

---

### Stop Container

```bash
docker stop <container>
