- [IBMCloud](#ibmcloud)
  - [Setting up the environement](#setting-up-the-environement)
    - [Install IBM Cloud CLI](#install-ibm-cloud-cli)
    - [Log in to IBMCloud CLI](#log-in-to-ibmcloud-cli)
    - [Install the IBM Cloud Kubernetes Service plug-in](#install-the-ibm-cloud-kubernetes-service-plug-in)
    - [Install the IBM Cloud Container Registry plug-in](#install-the-ibm-cloud-container-registry-plug-in)
    - [Install the Kubernetes CLI (kubectl)](#install-the-kubernetes-cli-kubectl)
    - [Install Docker](#install-docker)
  - [Provision a cluster](#provision-a-cluster)
  - [Set up and deploy an app](#set-up-and-deploy-an-app)

# IBMCloud

## Setting up the environement

### Install IBM Cloud CLI

- Mac and Linux : ```curl -sL https://ibm.biz/idt-installer | bash```
- Windows 10 : ```[Net.ServicePointManager]::SecurityProtocol = "Tls12"; iex(New-Object Net.WebClient).DownloadString('https://ibm.biz/idt-win-installer')```
- verify : ```ibmcloud dev help```

### Log in to IBMCloud CLI

- Log in to the IBM Cloud CLI: ```ibmcloud login -sso``` 

### Install the IBM Cloud Kubernetes Service plug-in

1. Create Kubernetes clusters and manage worker nodes by installing the IBM Cloud Kubernetes Service plug-in:```ibmcloud plugin install container-service -r Bluemix```
2. verify: ```ibmcloud plugin list``` shows as *container-service*

### Install the IBM Cloud Container Registry plug-in

1. IBM Cloud Container Registry plug-in ```ibmcloud plugin install container-registry -r Bluemix```
2. verify ```ibmcloud plugin list``` shows as container-registry.

### Install the Kubernetes CLI (kubectl) 

[Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-linux)

1. Download the latest release with the command: ```curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl```

### Install Docker

[Install docker](https://www.docker.com/community-edition#/download)

## Provision a cluster

- A cluster is a set of resources, worker nodes, networks, and storage devices that keep apps highly available. After you have your cluster, then you can deploy your apps in containers.

[Catalog](https://cloud.ibm.com/catalog)

- Enter a cluster name. Free clusters are deployed in the region that you select, but you cannot choose the location (data center).
- Click Create Cluster
- You can see the status of the worker node in the Worker nodes tab. 

## Set up and deploy an app

1. Clone or download the GitHub repository associated with this course.
2. go to Lab 1
3. ibmcloud login --sso
4. ibmcloud cr login
5. ibmcloud cr namespace-add <my_namespace>
6. docker build --tag us.icr.io/<my_namespace>/hello-world .
7. docker images
8. docker push us.icr.io/<namespace>/hello-world