# Kubernetes (K8s)

Kubernetes (K8s) is a container orchestration platform that provides capabilities such as clustering (running containers across multiple nodes), auto-scaling, auto-healing, and several production-grade features for managing containerized applications and microservices.

---

# Features of Kubernetes

## 1. Container Orchestration

Containers can be run individually using Docker, but managing hundreds of containers manually becomes difficult.

Kubernetes automatically:

- Schedules containers
- Distributes workloads across nodes
- Manages container lifecycle
- Maintains application availability

---

## 2. Auto-Healing

If a Docker container crashes, it must usually be restarted manually.

Kubernetes supports auto-healing through ReplicaSets and controllers by:

- Restarting failed containers
- Recreating missing Pods
- Maintaining the desired state automatically

---

## 3. Auto-Scaling

Instead of manually starting multiple containers, Kubernetes can automatically scale workloads based on:

- CPU utilization
- Memory usage
- Traffic demand

This is known as Horizontal Pod Autoscaling (HPA).

---

## 4. Load Balancing & Service Discovery

Kubernetes automatically distributes traffic across multiple Pods and provides built-in service discovery.

---

## 5. Rolling Updates & Rollbacks

Applications can be updated without downtime.

Benefits:

- Zero-downtime deployments
- Easy rollback to previous versions
- Controlled rollouts

---

## 6. Configuration & Secret Management

Kubernetes provides:

- ConfigMaps for non-sensitive configuration
- Secrets for sensitive information

---

## 7. Declarative Infrastructure

Desired state is defined using YAML manifests.

Example:

```yaml
replicas: 3
```

Kubernetes continuously works to maintain that state.

---

## 8. Multi-Node Cluster Management

Kubernetes manages workloads across multiple worker nodes from a central control plane.

---

## 9. Production-Grade Deployments

Provides enterprise capabilities such as:

- Auto-healing
- Auto-scaling
- Rolling updates
- Security controls
- High availability

---

# Kubernetes Architecture

Kubernetes consists of:

```text
Control Plane (Master)
          ↓
      Worker Nodes
```

---

# Control Plane Components (Master Node)

## API Server

The front door of Kubernetes.

Responsibilities:

- Accepts requests from users and tools
- Validates requests
- Updates cluster state
- Communicates with etcd

Sources of requests:

- kubectl
- Applications
- Controllers
- Other components

---

## Scheduler

Responsible for assigning Pods to worker nodes.

Scheduling decisions are based on:

- Available resources
- Policies
- Taints and tolerations
- Affinity and anti-affinity rules

---

## etcd

Distributed key-value database used to store:

- Cluster state
- Configuration data
- Resource definitions

Kubernetes cannot function without etcd.

---

## Controller Manager

Continuously compares:

```text
Desired State
      vs
Actual State
```

and takes corrective actions.

Example:

If 3 Pods are desired but only 2 exist, the controller creates another Pod.

---

## Cloud Controller Manager

Integrates Kubernetes with cloud providers such as:

- AWS
- Azure
- GCP

Manages:

- Load Balancers
- Storage
- Cloud networking

---

# Worker Node Components (Data Plane)

## Kubelet

Agent running on every worker node.

Responsibilities:

- Receives Pod specifications from API Server
- Starts containers
- Monitors container health
- Reports node status

---

## Kube-Proxy

Responsible for networking and service communication.

Functions:

- Maintains networking rules
