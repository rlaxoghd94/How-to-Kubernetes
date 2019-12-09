# Kubernetes Overview

## What is Kubernetes

Kubernetes is a portable, extensible, open-source platform for managing containterized workloads and services, that fasilitates both declarative configuration and automation. 

<br></br>

![deployment_era](./Resources/Deployment.png)

**Traditional deployment era**: Early on, organizations ran applications on physical servers. There was no way to define resource boundaries for applications in a physical server, and this caused resource allocation issues. For example, if multiple applications run on a physical server, there can be instances where one application would take up most of the resources, and as a result, the other application would underperform. A solution for this would be to run each application on a different physical server. But this did not scale as resources were underutilized, and it was expensive for organizations to maintina many physical servers.

**Virtualized depolyment era**: As a solution, virtualization was intorduced. It allows you to run multiple Virtual Machines on a single physical server's CPU. Virtualization allows applications to be isolated between VMs and provides a level of security as the information of one application cannot be freely accessed by another application.

Virtualization allows better utilization of resources in a physical server and allows better scalability because an application can be added or updated easily, reduces hardware costs, and much more. With virtualization you can present a set of physical resources as a cluster of disposable virutal machines.

Each VM is a full machine running all components, including its own operating system, on top of the virtualized hardware.

**Container deployment era**: Containers are similar to VMs, but they have relaxed isolation properties to share the Operating System among the applications. Therefore, containers are considered lightweight. Similar to a VM, a container has its own filesystem, CPU, memory, process space, and more. As they are decoupled from the underlying infrastructure, they are portable across clouds and OS distributions.

Containers have become popular because they provide extra benefits, such as:

- Agile application creation and deployment: increased ease and efficiency of container image creation compared to VM image use

- Continuous development, integration, and deployment: provides for reliable and frequent container image build and deployment with quick and easy rollbacks(due to image immutability)

- Dev and Ops separation of concerns: create application container images at build/release time rather than deployment time, thereby decoupling applications from infrastructure

- Observability not only surfaces OS-level information and metrics, but also application health and other signals

- Environmental consistency across development, testing, and production: Runs the same on a laptop as it does in the cloud

- Cloud and OS distribution portability: Runs on Ubuntu, RHEL, CoreOS, on-prem, Google Kubernetes Engine, and anywhere else

- Application-centric management: Raises the level of abstraction from running an OS on virtual hardware to running an application on an OS using logical resources

- Loosely coupled, distributed, elatic, liberated micro-services: applications are broken into smaller, independent pieces and can be deployed and managed dynamically - not a monolithic stack running on one big single-purpose machine

- Resource isolation: predicatble application performance

- Resource utilization: high efficiency and density

<br></br>

## Why you need Kubernetes and what can it do

Containers are a good way to bundle and run your applications. In a production environment, you need to manage the containers that run the applications and ensure that there is no downtime. For example, if a container goes down, another container needs to start. Wouldn't it be easier if this behavior was handled by a system?

That's how Kubernetes comes to the rescue! Kubernetes provides you with a framework to run distributed systems resiliently. It takes care of scaling and failover for your application, provides deployment patterns, and more. For example, Kubernetes can easily manage a canary deployment for your system.

Kubernetes provides you with:

- **Service discovery and load balancing**

  Kubernetes can exaplose a container using the DNS name or using their own IP address. If traffic to a container is high, Kubernetes is able to load balance and distribute the network traffic so that the deployment is stable

- **Storage orchestration**

  Kubernetes allows you to automatically mount a storage system of your choice, such as local storages, public cloud providers, and more

- **Automated rollouts and rollbacks**

  You can describe the desired state for your deployed containers using Kubernetes, and it can change the actual state to the desired state at a controlled rate. For example, you can automate Kubernetes to create new containers for your deployment, remove existing containers and adopt all their resources to the new container

- **Automatic bin packing**

  You provide Kubernetes with a cluster of nodes that it can be used to run containerized tasks. You tell Kubernetes how much CPU and memory(RAM) each container needs. Kubernetes can fit containers onto your nodes to make the best use of your resources

- **Self-healing**

  Kubernetes restarts containers that fail, replaces containers, kills containers that don't respond to your use-defined health check, and doesn't advertise them to clients until they are ready to serve

- **Secret and configuration management**

  Kubernetes lets you store and manage sensitive information, such as passwords, OAuth tokens, and SSH keys. You can deploy and update secrets and application configuration without rebuilding your container images, and without exposing secrets in your stack configuration

<br></br>

## What Kubernetes is not

Kubernetes is not a traditional, all-inclusive PaaS(Platform as a Service) system. Since Kubernetes operates at the container level rather than at the hardware level, it provides some generally applicable features common to PaaS offerings, such as deployment, scaling, load blanacing, logging, and monitoring. However, Kubernetes is not monolithic, and these default solutions are optional and pluggable. Kubernetes provides the building blocks for building develeoper platforms, but preserves use choice and flexibility where it is important.

Kubernetes:

- Does not limit the type of applications supported. Kubernetes aims to support an extremely diverse variety of workloads, including stateless, stateful, and data-processing workloads. If an application can run in a container, it should run greate on Kubernetes

- Does not deploy source code and does not build your application. Continuous Integration, Delivery, and Development(CI/CD) workflows are determined by organization cultures and preferences as well as techinical requirements

- Does not provide application-level services, such as middleware(for example, message buses), data-processing frameworks(for example, Spark), databases(for example, MySQL), caches, nor cluster storage systems(for example, Ceph) as built-in services. Such components can run on Kubernetes, and/or can be accessed by applications running on Kubernetes through portable mechanisms, such as the [Open Service Broker](https://openservicebrokerapi.org/)

- Does not dictate logging, monitoring, or alerting solutions. It provides some integrations as proof of concept, and mechanisms to collect and export metrics

- Does not provide nor mandate a configuration language/system(for example, Jsonnet). It provides a declarative API that may be targeted by arbitrary forms of declarative specifications

- Does not provide nor adopt any comprehensive machine configuration, maintenance, management, or self-headling systems

- Additionally, Kubernetes is not a mere orchestration system. In fact, it eliminates the ened for orchestration. The technical definition of orchestration is execution of a defined workflow: first do A, then B, then C. In contrast, Kubernetes comprises a set of independent composable control processses that continuously drive the current state towards the provided desired state. It shouldn't matter how you get from A to C. Centralized control is also not required. This results in a system that is easier to use and more powerful, robust, resilient, and extensible

<br></br>

## Kubernetes Components

When you deploy Kubernetes, you get a cluster

A cluster is a set of machines, called nodes, that run containerized applications managed by Kubernetes. A cluster has at least one worker node and at least one master node.

The worker node(s) host the pods that are the components of the application. The master node(s) manages the worker nodes and the pods in the cluster. Multiple master nodes are used to provide a cluster with fallover and high availability.

This part of the document outlines the various components you need to have a complete and working Kubernetes cluster.

Here's the diagram of a Kubernetes cluster with all the components tied together.

![Components](./Resources/Components.png)

<br></br>

## Master Components

Master components provide the cluster's control plane. Master components make global decisions about the cluster(for example, scheduling), and they detect and respond to cluster events(for example, starting up a new pod when a deployment's `replicas` field is unsatisfied).

Master components can be run on any machine in the cluster. However, for simplicity, set up scripts typically start all master components on the same machine, and do not run user containers on this machine. See [Building High-Availability Clusters](https://kubernetes.io/docs/admin/high-availability/) for an example multi-master VM setup.

### kube-apiserver

The API server is a component of the Kubernetes control plane that exposes the Kubernetes API. The API server is the front end for the Kubernetes control plane.

The main implementation of a Kubernetes API server is [kube-apiserver](https://kubernetes.io/docs/reference/generated/kube-apiserver/). kube-apiserver is designed to scale horizontally - that is, it scales by deploying more instances. You can run several instances of kube-apiserver and balance traffic between those instances.

### etcd

Consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.

If your Kubernetes cluster uses etcd as its backing sotre, make sure you have a [back up](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster) plan for those data.

You can find in-depth information about etcd in the official [documentation](https://etcd.io/docs/)

### kube-scheduler

Component on the master that watches newly created pods that have no node assigned, and selects a node for them to run on.

Factors taken into account for scheduling decisions include individual and collective rsource requirements, hardware/sofware/policy constraints, affinity and anti-affinity specifications, data locality, inter-workload interference and deadlines.

### kube-controller-manager

Component on the master that runs controllers.

Logically, each controller is a separate process, but to reduce complexity, they are all compiled into a single binary and run in a single process.

These controllers include:

- Node Controller: Responsible for noticing and responding when nodes go down

- Replication Controller: Responsible for maintaining the correct number of pods for every replication controller object in the system

- Endpoints Controller: Populates the Endpoints object(that is, joins Services & Pods).

- Service Account & Token Controllers: Create default accounts and API access tokens for new namespaces

### cloud-controller-manager

[cloud-controller-manager](https://kubernetes.io/docs/tasks/administer-cluster/running-cloud-controller/) runs controllers that interact with the underlying cloud providers. The cloud-controller-manager binary is an alpha feature introduced in Kubernetes release 1.6.

cloud-controller-manager runs cloud-provider-specific controller loops only. You must disable these controller loops in the kube-controller-manager. You can disable the controller loops by setting the `--cloud-provider` flags to `external` when starting the kube-controller-manager.

cloud-controller-manager allows the cloud vendor's code and the Kubernetes code to evolve independently of each other. In prior releases, the core Kubernetes code was dependent upon cloud-provier-specific code for functionality. In future releases, code specific to cloud vendors should be maintained by the cloud vendor themselves, and linked to cloud-controller-manager while running Kubernetes.

The following controllers have cloud provider dependencies:

- Node Controller: For checking the cloud provider to determine if a node has been deleted in the cloud after it stops responding

- Route Controller: For setting up routes in the underlying cloud infrastructure

- Service Controller: For creating, updating and deleting cloud provider load balanacers

- Volume Controller: For creating, attaching, and mounting volumes, and interacting with the cloud provider to orchestrate volumes

<br></br>

## Node Components

Node components run on every node, maintaining running opds and providing the Kubernetes runtime environment

### kubelet

An agent that runs on each node in the cluster. It makes sure that containers are running in a pod.

The kubelet takes a set of PodSpecs that are provided through various mechanisms and ensures that the containers described in those PodSpecs are running and healty. The kubelet doesn't manage containers which were not created by Kubernetes.

### kube-proxy

kube-proxy is a network proxy that runs on each node in your cluster, implementing part of the Kubernetes Service concept.

kube-proxy maintains network rules on nodes. These network rules allow network communication to your Pods from network sessions inside or outside of your cluster.

kube-proxy uses the operating system packet filtering layer if there is one and it's available. Otherwise, kube-proxy forwards the traffic itself.

### Container Runtime

The container runtime is the software that is responsible for running containers.

Kubernetes supports several container runtimes: Docker, containerd, cri-o, rktlet and any implementation of the Kubernetes CRI(Container Runtime Interface).

<br><br/>

## Addons

Addons use Kubernetes resources(DaemonSet, Deployment, etc) to implement cluster features. Because these are providing cluster-level features, namespaced resources for addons belong within the `kube-system` namespace.

Selected addons are described below.

### DNS

While the other addons are not strictly required, all Kubernetes clusters should have cluster DNS, as many examples rely on it.

Cluster DNS is a DNS server, in addition to the other DNS server(s) in your environment, which serves DNS records for Kubernetes services.

Containers started by Kubernetes automatically include this DNS server in their DNS searches.

### Web UI (Dashboard)

Dashboard is a general purpose, web-based UI for Kubernetes clusters. It allows users to manage and troubleshoot applications running in the cluster, as well as the cluster itself.

### Container Resource Monitoring

Container Resource Monitoring records generic time-series metrics about containers in a central database, and provides a UI for browsing that data.

### Cluster-level Logging

A cluster-level logging mechanism is responsible for saving container logs to a central log store with search/browsing interface.