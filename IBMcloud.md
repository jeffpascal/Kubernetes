
## Set up environement

### Install the IBM Cloud command-line interface (CLI)

Install the IBM Cloud command-line interface. After it’s installed, you can access IBM Cloud from your command line with the prefix ibmcloud.

Log in to the IBM Cloud CLI: ibmcloud login.

For a federated ID: If you have a federated ID, use ibmcloud login –sso to log in to the IBM Cloud CLI. Enter your user name and use the provided URL in your CLI output to retrieve your one-time passcode. You know you have a federated ID if the login fails without the –sso and succeeds with the –sso option.

### Install the IBM Cloud Kubernetes Service plug-in

Create Kubernetes clusters and manage worker nodes by installing the IBM Cloud Kubernetes Service plug-in:

ibmcloud plugin install container-service -r Bluemix

Tip: The prefix for running commands by using the IBM Cloud Kubernetes Service plug-in is ibmcloud cs.

Verify that the plug-in is installed properly:

ibmcloud plugin list

The plug-in is displayed in the results as container-service.

### Install the IBM Cloud Container Registry plug-in

Use this plug-in to set up your own namespace in a multi-tenant, highly available, and scalable private image registry that is hosted by IBM, and to store and share Docker images with other users. Docker images are required to deploy containers into a cluster.

**Tip:** The prefix for running registry commands is ```ibmcloud cr.```

Manage a private image repository by installing the IBM Cloud Container Registry plug-in:

```
ibmcloud plugin install container-registry -r Bluemix
```

Verify that the plug-in is installed properly:

```
ibmcloud plugin list
```

The plug-in is displayed in the results as container-registry.

### Install the Kubernetes CLI (kubectl)

Install the version of [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) that is the same [Kubernetes version for IBM Cloud Container Service](https://console.bluemix.net/docs/containers/cs_versions.html#cs_versions). Using an older kubectl with a newer server might produce validation errors.

### Install Docker

To locally build images and push them to your registry namespace, [install Docker](https://www.docker.com/community-edition#/download). The Docker CLI is used to build applications into images.

## Provision a cluster

- Before you dive into Kubernetes, you need to provision a cluster for your containerized app. Then, you won’t need to wait for it to be ready for the labs in this course. A cluster is a set of resources, worker nodes, networks, and storage devices that keep apps highly available. After you have your cluster, then you can deploy your apps in containers.

- Be sure you installed the CLIs as described previously.

- Provisioning a cluster can take a few minutes.

- Go to the [IBM Cloud catalog](https://console.bluemix.net/catalog/) and provision a Kubernetes cluster.
Enter a cluster name. Free clusters are deployed in the region that you select, but you cannot choose the location (data center).
Click Create Cluster.

- The worker node in the cluster takes a few minutes to provision. You can see the status of the worker node in the Worker nodes tab. When the status reaches Ready, your worker node is ready to be used.

## Containers

- Containers provide isolation similar to virtual machines (VMs), except provided by the OS and at the process level. Each container is a process or group of processes that are run in isolation. Typical containers explicitly run only a single process because they have no need for the standard system services. What they usually need to do can be provided by system calls to the base OS kernel.

- The isolation on Linux is provided by a feature called namespaces. Each different kind of isolation, that is, user and cgroups, is provided by a different namespace.

- This is a list of some of the namespaces that are commonly used and visible to the user:

```
PID: process IDs
USER: user and group IDs
UTS: host name and domain name
NS: mount points
NET: network devices, stacks, and ports
CGROUPS: control limits and monitoring of resources_
```

- Containers allow you to share the host OS. This reduces duplication while still providing the isolation. Containers also allow you to drop unneeded files such as system libraries and binaries to save space and reduce your attack surface. If SSHD or LIBC are not installed, they cannot be exploited.

## Kubernetes

- Kubernetes is a container orchestrator to provision, manage, and scale apps. In other words, Kubernetes allows you to manage the life cycle of containerized apps in a cluster of nodes (which are a collection of worker machines, for example, VMs or physical machines).

- Your apps might need many other resources to run such as Volumes, Networks, and Secrets that will help you to do things such as connect to databases, talk to firewalled back ends, and secure keys. Kubernetes helps you add these resources into your app. Infrastructure resources needed by apps are managed declaratively.
    - Like Apache Mesos and Docker Swarm, Kubernetes is a container orchestrator to provision, manage, and scale apps.

- The key paradigm of Kubernetes is its declarative model. The user provides the desired state and Kubernetes will do its best to make it happen. If you need five instances, you do not start five separate instances on your own but rather tell Kubernetes that you need five instances, and Kubernetes will reconcile the state automatically. At this point, you simply need to know that you declare the state that you want and Kubernetes makes that happen. If something goes wrong with one of your instances and it crashes, Kubernetes still knows the desired state and creates new instances on an available node.

- Fun to know: Kubernetes goes by many names. Sometimes it is shortened to k8s (losing the internal 8 letters), or kube. The word is rooted in ancient Greek and means “Helmsman.” A helmsman is the person who steers a ship. We hope you can see the analogy between directing a ship and the decisions made to orchestrate containers on a cluster.

### Kubernetes, open-sourced orchestration

- Kubernetes is developed by the community with the intent of addressing container scaling and management needs. So in the early days of Kubernetes, the community contributors leveraged their knowledge of creating and running internal tools, such as Borg and Omega. With the advent of the Cloud Native Computing Foundation (CNCF), the community adopted Open Governance for Kubernetes. IBM, as a founding member of CNCF, actively contributes to CNCF's cloud-native projects, along with other companies such as Google, Red Hat, Microsoft, and Amazon.

### Kubernetes architecture

- At its core, Kubernetes is a data store (etcd). The declarative model is stored in the data store as objects, and that means when you say you want five instances of a container, then that request is stored into the data store. This information change is watched and delegated to Controllers to take action. Controllers then react to the model and attempt to take action to achieve the desired state. The power of Kubernetes is in its simplistic model.

- In the following diagram, an API server is a **simple HTTP server handling create/read/update/delete (CRUD)** operations on the data store. The Controller identifies the change that you want and makes that happen.

- Controllers are responsible for instantiating the actual resource represented by any Kubernetes resource. These actual resources are what your app needs to allow it to run successfully.

![image](images/kubernetes-architecture)

