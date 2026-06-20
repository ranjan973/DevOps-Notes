K8s is a container orchestration platform which provides functionalities like clustering ( runnining containers on multiple nodes), auto-scaling , auto healing and several production grade features to use containers or microservices. 

### Features of K8s

1) Cantainer Orchestration : You can run containers using docker run but managing 100s of containers manually is hard. K8s automatically schedule, manages and maintain containers across multiple nodes and distributes them equally.

2) Auto Healing - If a container dies in docker, it needs to be manually started. K8s supports auto-healing and automatically restarts failed containers using replicaset feature which maintains the desired state.

3) Auto-Scaling - Run multiple dokcer run commands. K8s suupport horizontal auto-scaling based on CPU,traffic and memory.

4) Load Balancing & Service Discovery
5) Rolling Updates & Rollbacks
6) Configuration & secret management using ConfigMaps and secrets.
7) Declarative infrastructure
8) Multinode cluster management
9)Production grade deployments.

### K8s Architecture components

Control Plane ( Master Node) :

- API Server - This is the front door of Kubernetes which receives requests from users, kubectl and other components, validates them and update the cluster state usually by writing that desired state to etcd.

- Scheduler - The scheduler watches for new pods which do not have a node assigned and decides which worker node should they run on based on resources, policies,taints/tolerations, affinity rules etc

- Etcd - Distributed key vaule store used by K8s to store the cluster state and configurations.

- Controller Manager - Continously monitors the cluster state and make sure that the actual state matches the desired state. Ex: The deployment controller ensures that the desired number of PODS are running

- Cloud Controller Manager


Data Plane ( Worker Nodes)

- Kubelet - It is an agent running on each worker node. Receives Pods specifications from the API server and ensures that the containers on those pods are running as expected and the resources they need are available. 

- Kube-proxy - Manages network rules on each node and make sure that the Pods can communicate with each other and with Services. 
Kubeproxy works by maintaining a set of network rules on each node in a cluster which are updated dynamically as services are added and removed. When the client sends a request to a service, it is intercepted by the kube-proxy on the node where it was received. Kube-Proxy then looks at the destination endpoint for the service and then routes the request accordingly. 

Kube-Proxy is an essential component of the K8s cluster as it ensures that services can communicate with each other.

- Container Runtime


 WHAT WE LEARNED:
✅ What is a Pod and why it exists
✅ What is kubectl (Kubernetes CLI)
✅ How to install kubectl and Minikube
✅ Deploy first application as a Pod
✅ Debug and troubleshoot Pods

🔑 KEY CONCEPTS:

What is a Pod?
- Smallest unit in Kubernetes
- Wrapper around container(s)
- Uses YAML file (not command line like Docker)
- Gets IP address from Kubernetes

Why Pods instead of containers?
- Standardization (everyone uses YAML)
- Easy to manage 100s of containers
- Version control (store in Git)
- Declarative approach

What is kubectl?
- Command line tool for Kubernetes
- Like "docker" command but for Kubernetes
- Used to create, delete, debug Pods

Pod = 1 or More Containers:
- Usually: 1 Pod = 1 Container
- Sometimes: 1 Pod = Multiple Containers (sidecar pattern)
- Containers in same Pod share IP and storage

⚡ INSTALLATION STEPS:

1. Install kubectl (Kubernetes CLI)
   - Linux/Mac/Windows - different commands
   - Verify: kubectl version --client

2. Install Minikube (local Kubernetes)
   - Download from minikube.sigs.k8s.io
   - Verify: minikube version

3. Start Kubernetes cluster
   - Command: minikube start
   - Creates single-node cluster
   - Verify: kubectl get nodes

4. Create pod.yaml file
   - Define Pod specifications
   - Image, ports, name, etc.

5. Deploy Pod
   - kubectl create -f pod.yaml
   - Check: kubectl get pods

6. Access application
   - Method 1: minikube ssh → curl <pod-ip>
   - Method 2: kubectl port-forward pod/nginx 8080:80

🔧 IMPORTANT KUBECTL COMMANDS:

Create Pod:
kubectl create -f pod.yaml

List Pods:
kubectl get pods
kubectl get pods -o wide (with IP address)

Debug Pod:
kubectl describe pod <name> (detailed info)
kubectl logs <name> (application logs)

Execute inside Pod:
kubectl exec it <name> - /bin/bash

Delete Pod:
kubectl delete pod <name>

Stop cluster:
minikube stop

💡 DOCKER VS KUBERNETES:

Docker way:
docker run -d --name nginx -p 80:80 nginx:1.14.2

Kubernetes way:
Create YAML file with all specifications
kubectl create -f pod.yaml

🎓 FOR INTERVIEWS:

Q: What is a Pod?
A: Smallest deployable unit in Kubernetes. Wrapper around containers with YAML specifications.

Q: How to debug Pods?
A: kubectl describe pod <name> (status/events)
   kubectl logs <name> (application logs)

Q: Pod vs Deployment?
A: Pod = Basic unit (no auto-healing)
   Deployment = Production-ready (auto-healing, auto-scaling)

📊 POD vs DEPLOYMENT (Preview):

Pod:
- Manual creation
- No auto-restart if crashes
- No auto-scaling
- Good for learning

Deployment (Tomorrow's topic!):
- Automatic management
- Auto-healing (restarts crashed Pods)
- Auto-scaling (creates more Pods)
- Production-ready!

⚠ IMPORTANT NOTES:

✅ Minikube = FREE (no AWS charges!)
✅ Single-node cluster (1 Master + 1 Worker combined)
✅ Perfect for learning and practice
✅ In production, use Deployments (not Pods directly)
✅ Reference: kubectl cheat sheet on kubernetes.io

ReplicaSet Controller – A ReplicaSet is a Kubernetes controller responsible for maintaining the desired number of Pod replicas. It automatically scales Pods up or down and ensures the actual state of the cluster matches the desired state defined in the configuration. In practice, ReplicaSets are usually managed indirectly through Deployments rather than being used independently.


Deployment – A Deployment is a higher-level Kubernetes object that manages ReplicaSets and Pods. In addition to scaling capabilities, it provides advanced features such as rolling updates, rollbacks, and controlled application deployments, making it the preferred approach for managing stateless applications in Kubernetes.

Daemonset controller - A Kubernetes DaemonSet is a workload controller that ensures a copy of a specific Pod runs on all (or a selected subset of) worker nodes within a cluster. As new nodes are added to the cluster, the DaemonSet automatically spins up new Pods on them, and when nodes are removed, those Pods are safely garbage collected.

Use Cases: Log Collection agents from each host, Cluster Monitoring , Cluster Networking(kube-proxy)

Service: Kubernetes Service solves the problem of dynamic Pod IPs by providing stable networking, service discovery, and load balancing for Pods.
Pods are ephemeral in nature. When Pods die, it may come up with new IPs and if an application tries to connect using teh POD Ip, it can break anytime.

A service provide:
- DNS Name
- Load Balancing
- Service Discovery using Labels and Selector

Types of Service:

- Cluster IP Mode : Used for INternal communication within the cluster. The service gets an internal Virtul IP which is accessible only inside K8s.
Ex: Frontend Pod → Backend Service → Backend Pods 
Backend should not be exposed to internet. Internal only. Default service type

-- Node Port Mode: K8s opens a port on every worker node. <Node IP: Port>.
Use Case: Testing, small environments, Lab Setups
Accessible externally. Port range usually 30000-32767
When a service is created as a node-port type, the kube-proxy updates the IPTables with NOde IP address and the port number that is chosen in the service config file to access the pods.

-- Load Balancer Mode: Expose application externally using a cloud provider load balancer. Managed by Cloud Controller Manager. A loadbalancer automatically gets created on teh cloud platform . Best for prod external access . Cloud-Managed load balancing
Used in:

AWS
Azure
GCP

Namespace in Kubernets is a logical isolation of resources, network poliices, rbac and everything. Ex: Two projects can use the same K8s cluster in different namespaces without any overlap and authentication probelms.

Kubernetes Ingress: 

The Problem: In Kubernetes, internal microservices are isolated by default, with dynamic, changing IP addresses. If you have multiple applications (like a web app at example.com and an API at ://example.com) and rely on basic K8s Services, you would need to provision a separate, expensive external load balancer for each microservice

The Solution: Kubernetes Ingress solves this by acting as a central smart router and reverse proxy. Instead of exposing every single microservice to the internet, Ingress provides a single entry point (typically one external IP address) that routes external traffic inside the cluster based on URL paths or domain names.

How Ingress Works:

An Ingress implementation relies on two parts to solve cluster routing:

The Ingress Resource: A declarative API blueprint (defined as YAML) that contains routing rules. For example:Route ://example.com to the frontend-service.Route ://example.com to the backend-service.

The Ingress Controller: The actual software implementation (like NGINX, Traefik, or HAProxy) that monitors the Ingress Resource and enforces those traffic rules.

Core Problems Solved by Ingress:

Path & Host-Based Routing: Allows you to direct traffic for multiple microservices based purely on URL paths (e.g., /blog vs /cart) or subdomains.

Centralized TLS/SSL Termination: Manages HTTPS certificates in one place so you don't have to configure secure connections for every individual service.

Cost Efficiency: Consolidates all inbound web traffic through a single Cloud Load Balancer.

IMP Note: While Ingress has been a foundational Kubernetes object, the open-source community is transitioning toward its successor, the Kubernetes Gateway API, which offers more advanced traffic routing and better role-based delegation.

*** Custome Resource in K8s

Problem it solves:

Kubernetes only understands the built in resources/objects like pods, deployments, services etc but real world systems need domain-specific objects like database, ML Model etc. Custom resource helps extend the functionality of K8s by adding your own resource types.

Just like built in K8s objects, customer resources need Customer Resource Definition (CRD) which is a blue-print or schema of a the new custom resource type. It contains all the instructions which will be needed to write a custom resource. Ex:

kind: Database
spec:
  engine: postgres

After CRD is applied,K8s understands database and we can run command like kubectl get database.CRD is only data (schema), not logic

-- CR Controller -> Its the brain and the program running inside the cluster and continously ensures Desired state = Actual state by watching resources, comparing the states and take action.

With the controller, the custom resource is just a YAML file. “CRDs define the resource, controllers implement the logic to reconcile and manage it.

*** Kubernetes RBAC --> Kubernetes Authorization System
-- Who can do what , ON which resource , IN which SCOPE (namespace/cluster)

RBAC controls access to Kubernetes resources based on roles assigned to users or workloads. RBAC solves the problem of uncontrolled access by enforcing fine-grained, least-privilege authorization in Kubernetes.

Core Components of RBAC:

1) Subjects (WHO) - User, Groups , Service Accounts ( Identity for Pods). Pods use service accounts to talk to API Server.

2)Roles (WHAT Permissions)
	* Role (namespace scoped) - Works only within a namespace . Ex:
		resources: ["pods"]
		verbs: ["get", "list"]
		This ex only reads Pods in that namespace.
		
	ClusterRole (Cluster wide)
	* Works across entire cluster pr for cluster level resources.
	* Can also be reused across namespaces.
	
3) Bindings ( WHo get access) : Connect a subject to the role.
	
	* RoleBinding : Assigns role/clusterRole to users, groups, SA 
		Scope = namespace
	* ClusterRoleBinding : Assigns Cluster Role , scope = entire cluster
	
Inside Role (Rules)
Each Role has rules:
 apiGroups - WHich API
 resources - Pods, Services, etc
 verbs - Actions

Common verbs below:

get (read), list,watch,create,update,delete,patch

Step 1: Create Role.

kind: Role
rules:
- resources: ["pods"]
  verbs: ["get", "list"]
  
Step 2: Bind Role

kind: RoleBinding
subjects:
- kind: User
  name: dev-user

#### Kubernetes ConfigMap & Secret

ConfigMap stores app configuration, Secret stores sensitive data, both are injected into pods via environment variables or volumes to avoid hardcoding values.

What problems do these solve??

While deploying apps in K8s, we need configuration (URLs, ports, env vars) and sensitive data (passwords, API keys)and storing these values directly in the code is a bad approach. We will have to rebuild containers every time these value changes because we can't change env vars directly in PODs, it's not environment friendly ( dev/test/prod) and security risk of exposing teh credentials.

K8s solve this by :
ConfigMap -> Non sensitive data
Secret -> Sensitive data

What is ConfigMap - A ConfigMap is a Kubernetes API object used to store non-confidential configuration data in key-value pairs. It allows you to decouple environment-specific settings from your application code, keeping your container images portable and reusable across development, testing, and production environments. Ex:

apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: "production"
  LOG_LEVEL: "info"
  
  What is Secret -> A Kubernetes Secret is a built-in object designed to store and manage small amounts of sensitive data—such as passwords, tokens, API keys, and certificates—separately from your application code. By using Secrets, you avoid hardcoding credentials into your container images or manifest files, reducing the risk of accidental exposure in public version control systems like GitHub. Values are Base64 encoded (not encryption by default)and encryption handling can be done outside K8s like Hashicorp vault etc.
  
  Types of Secrets:
  
  - Opaque: The default type used to store general, arbitrary user-defined data.
  - kubernetes.io/tls: Specifically used for storing TLS certificates and private keys to secure communications.
  - kubernetes.io/service-account-token: Carries a token identifier used to authenticate service accounts with the API server.
  - kubernetes.io/basic-auth: Used for storing credentials required for standard HTTP basic authentication.
  - kubernetes.io/ssh-auth: Used for storing SSH credentials and private keys.
  - kubernetes.io/dockerconfigjson: Stores credentials for a Docker registry to let Kubernetes pull private container images
  
Ex: 

apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=     # admin
  password: cGFzcw==     # pass


 How Pods Use ConfigMap / Secret?
 
 1) Environment Variables: The secret values are injected into the container's environment when it starts up.
 
 2) Mounted Volumes: The secret keys are mounted into the container's file system as files inside a specific directory. This is often preferred because updates to the secret can automatically sync to the files without restarting the container.
 
 Step 1: Create ConfigMap/Secret
 
 apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  app.properties: |
    APP_ENV=production
    LOG_LEVEL=info
	
Step 2: Mount in Pod as volumes

apiVersion: v1
kind: Pod
metadata:
  name: configmap-volume-demo
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: app-config
	  
Production Best Practices:

Because default Secrets lack strong native security, enterprise environments rely on additional safeguards:

- Enable Encryption at Rest: Configure the Kubernetes API server to encrypt data before writing it to etcd.
- Strict RBAC Controls: Restrict who can view or modify Secrets using standard Kubernetes Role-Based Access Control (RBAC) configurations.
- External Secret Managers: Integrate Kubernetes with third-party tools like HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault using plugins like the External Secrets Operator.
- GitOps Tools: Use tools like Sealed Secrets to encrypt your credentials before pushing your configuration manifests to Git repositories.
	


kubectl create namespace monitoring
helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring

Since we have minikube and if we expose the service as nodeport, it maps the service to the minikube IP which is not accessible from teh browser, we will use port-forwarding to route traffic from 192.168.11.215:8082 to the prometheus/grafana pod listening on port 9090/80

kubectl port-forward -n monitoring svc/prometheus-grafana --address 0.0.0.0 8082:80

### Helm and Helm Charts

As microservices multiply, each with their own Deployments, Services, ConfigMaps, and Ingresses, it becomes painfully clear that manually managing these manifests is error-prone, time-consuming, and downright unwieldy. This is where Helm, known as the “package manager for Kubernetes,” comes in. Helm streamlines Kubernetes management by grouping sets of related manifests into convenient units called charts, making deployments reproducible, configurable, and version-controlled. Instead of treating your Kubernetes configs as an unwieldy tangle of YAML, you treat them as modular, parameterized packages.

Why Helm?

1) The Manifest Hell Problem
2) The missing Package Manager of K8s
3) Configuration and Reusability

Charts: A Helm chart is essentially a package of pre-configured Kubernetes resources. It’s a directory that contains templates (Kubernetes manifests with placeholders), default values, metadata about the chart (like its Chart.yaml), and somet`s like NOTES.txt for release information.

Templates and Values: At the hart of Helm is the idea of templates. These are Kubernetes manifests sprinkled with Go templating syntax. Helm injects values from values.yaml or command-line overrides, transforming templates into valid Kubernetes manifests ready for deployment.

Releases: When you install a chart into a Kubernetes cluster, Helm creates a release, which is a running instance of that chart. Each release has a name (like polar-bear or spinning-windmill) and tracks the installed version of the chart, as well as the chart’s deployed manifests.

Repositories: Helm charts can be stored in repositories, much like code packages. Public repositories like the Artifact Hub host thousands of community charts. Organizations often maintain private repos for in-house charts, ensuring a consistent set of applications and components.

Helm CLI Basics: The Helm command-line interface (helm) is your primary tool. Common operations include:

helm repo add – Add a chart repository
helm search repo – Search for charts in a repo
helm install – Install a chart into a cluster, creating a release
helm upgrade – Upgrade a release to a new version of a chart
helm rollback – Rollback a release to a previous version
helm uninstall – Remove a release from the cluster

Refer : https://medium.com/@ksaquib/what-the-heck-are-helm-charts-a-deep-dive-into-the-kubernetes-package-manager-0512644eb3ed
