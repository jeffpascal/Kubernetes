
- [Download](#download)
  - [Clone project](#clone-project)
  - [Build the docker file](#build-the-docker-file)
  - [Check cluster](#check-cluster)
  - [Set up environement variable and download configuration files](#set-up-environement-variable-and-download-configuration-files)
  - [Expose that deployment as a service](#expose-that-deployment-as-a-service)
    - [Cleaning up](#cleaning-up)
  - [Expose deployment](#expose-deployment)
  - [Get details of deployment](#get-details-of-deployment)
    - [Clean up](#clean-up)
    - [Get all services](#get-all-services)
    - [Delete a service](#delete-a-service)
- [Scale apps with replicas](#scale-apps-with-replicas)
  - [Update the replica set](#update-the-replica-set)

## Download

### Clone project 
1. ```git clone```
2. mvn clean install to get target/
3. edit pom.xml add:
   ```<finalName>docker-spring-boot</finalName>```
   in build to make the jar name easier

4. Create the dockerfile
5. in application.properties add ```server.port=8085```

1. go to docker hub, identify what images you need
2. write docker file

```
FROM openjdk:8
ADD target/docker-spring-boot.jar docker-spring-boot.jar
EXPOSE 8085
ENTRYPOINT ["java", "-jar","docker-spring-boot.jar"]
```

- ADD add the built file to the Container, ADD <from> <to>, "<from>" is your target folder after ```maven clean install``` and <to> is your destination in your container. So basicalli the same arguments as ```mv <from> <to>``` which can also be used for renaming or moving

- EXPOSE expose the port 8085
- run the command to run the jar. ```java -jar ./target/docker-spring-boot.jar```  and you need to execute it in the container

### Build the docker file

1. ```docker build --tag us.icr.io/jeffpascalnamespace/spring-app .```

2. ```docker push us.icr.io/jeffpascalnamespace/spring-app```

### Check cluster

1. ```ibmcloud cs clusters```
  
2. ```ibmcloud cs workers <yourclustername>```
   - note public ip address

### Set up environement variable and download configuration files

- Get the command to set the environment variable and download the Kubernetes configuration files:
```ibmcloud cs cluster-config <cluster_name_or_ID>```
    - this needs to be done if you have access denied error
non-depracated version : ```ibmcloud ks cluster config mycluster```
- a command is displayed that you can use to set the path to the local Kubernetes configuration file as an environment variable
- set the KUBECONFIG environment variable.

### Run your image as deployement

- Run your image as a deployment:
```kubectl run <app-name> --image=us.icr.io/<namespace>/<app-name>```
- ```kubectl get pods``` to see the status

### Expose that deployment as a service

- ```kubectl expose deployment/<app-name> --type="NodePort" --port=8080```
- Find the port that is used on that worker node and examine your new service
```kubectl describe service <name-of-deployment>```

- note the public ip
```ibmcloud cs workers <name-of-cluster>```


#### Cleaning up

- delete deployment ```kubectl delete deployment <app-name>```

### Expose deployment

- ```kubectl expose deployment/spring-app --type="NodePort" --port=8085```
  - You can now access your container/service by using curl <public-IP>:<nodeport> (or a web browser).
  
### Get details of deployment

- ```kubectl describe service spring-app```
  - note NodePort

#### Clean up

#### Get all services

- ```kubectl describe services```

#### Delete a service

- ```kubectl delete service spring-app```

## Scale apps with replicas

- A replica is how Kubernetes accomplishes scaling out a deployment. A replica is a copy of a pod that already contains a running service. By having multiple replicas of a pod, you can ensure your deployment has the available resources to handle increasing load on your app.

### Update the replica set

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
