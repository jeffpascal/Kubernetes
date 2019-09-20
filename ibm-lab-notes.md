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
    - [Push an image to the IBM Cloud container registry](#push-an-image-to-the-ibm-cloud-container-registry)
    - [Deploy your application](#deploy-your-application)
      - [Set up environement variable and download configuration files](#set-up-environement-variable-and-download-configuration-files)
      - [Expose that deployment as a service](#expose-that-deployment-as-a-service)
  - [Scale and update apps Services/replica sets/health-checks](#scale-and-update-apps-servicesreplica-setshealth-checks)
    - [Scale apps with replicas](#scale-apps-with-replicas)
      - [Update the replica set](#update-the-replica-set)

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

### Push an image to the IBM Cloud container registry

1. Clone or download the [GitHub repository](https://github.com/IBM/container-service-getting-started-wt) associated with this course.
2. go to Lab 1
3. ibmcloud login --sso
4. This will allow you to push images to the IBM Cloud Container Registry. ```ibmcloud cr login```
5. Create a namespace in IBM Cloud Container Registry where you can store your images: ```ibmcloud cr namespace-add <my_namespace>```
6. Build the example Docker image: ```docker build --tag us.icr.io/<my_namespace>/hello-world .```
7. docker images
8. Push that image to IBM Cloud Container Registry: ```docker push us.icr.io/<namespace>/hello-world```
9. ```ibmcloud cs clusters``` to make sure the cluster is in Normal state
10. ```ibmcloud cs workers <yourclustername>``` make sure all workers are in Normal state with Ready status

 173.193.92.31 public ip

### Deploy your application

#### Set up environement variable and download configuration files

- Get the command to set the environment variable and download the Kubernetes configuration files:
```ibmcloud cs cluster-config <cluster_name_or_ID>```
- a command is displayed that you can use to set the path to the local Kubernetes configuration file as an environment variable
- set the KUBECONFIG environment variable.

#### Run your image as deployement

- Run your image as a deployment:
```kubectl run hello-world --image=us.icr.io/<namespace>/hello-world```

- Check status : ```kubectl get pods```

#### Expose that deployment as a service

- kubectl expose deployment/hello-world --type="NodePort" --port=8080
- Find the port that is used on that worker node and examine your new service
```kubectl describe service <name-of-deployment>```

- note the public ip
```ibmcloud cs workers <name-of-cluster>```

- You can now access your container/service by using curl <public-IP>:<nodeport> (or a web browser).

- delete deployment ```kubectl delete deployment hello-world```

## Scale and update apps Services/replica sets/health-checks

### Scale apps with replicas

- A replica is how Kubernetes accomplishes scaling out a deployment. A replica is a copy of a pod that already contains a running service. By having multiple replicas of a pod, you can ensure your deployment has the available resources to handle increasing load on your app.

#### Update the replica set

- ```kubectl edit deployment/<name-of-deployment>```

- change replicas number with the editor then save and exit

``` ...
    spec:
      replicas: 1
      selector:
        matchLabels:
          run: hello-world
      strategy:
        rollingUpdate:
          maxSurge: 1
          maxUnavailable: 1
        type: RollingUpdate
      template:
        metadata:
          creationTimestamp: null
          labels:
            run: hello-world
    ...
```

- check the status of the roll-out ```kubectl rollout status deployment/<name-of-deployment>```

- ensure pods are running with : ```kubectl get pods```

- Tip: Another way to improve availability is to add clusters and regions to your deployment as shown in the following diagram:

![alt](images/kluster-high-availability.PNG)



