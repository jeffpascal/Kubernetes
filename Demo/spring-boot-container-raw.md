# Main

1. git clone https://github.com/jeffpascal/Spring-and-springboot.git
   1. git clone --single-branch --branch demo https://github.com/jeffpascal/Spring-and-springboot.git
2. mvn clean install
3. create Dockerfile
```
FROM openjdk:8
ADD target/docker-spring-boot.jar docker-spring-boot.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar","docker-spring-boot.jar"]
```
4. ```docker build --tag us.icr.io/jeffpascalnamespace/spring-app .```
5. ```docker push us.icr.io/jeffpascalnamespace/spring-app```
6. ```ibmcloud cs clusters```
7. ```ibmcloud cs workers mycluster```
    - note public ip address
8. ```ibmcloud cs cluster-config mycluster```
9. ```kubectl run spring-app --image=us.icr.io/jeffpascalnamespace/spring-app```
10. ```kubectl get pods```
11. ```kubectl expose deployment/spring-app --type="NodePort" --port=8080```
12. ```kubectl describe services```
13. scale ```kubectl edit deployment/spring-app```
14. ```docker build --tag us.icr.io/jeffpascalnamespace/spring-app:1 .```
15. ```docker push us.icr.io/jeffpascalnamespace/spring-app:2```
16. ```kubectl edit deployment/spring-app```
17. ```kubectl rollout status deployment/spring-app```
18. ```kubectl get replicasets```

## Controls

- delete image docker

```docker image rm 09eaebc81439```

- get workers

```ibmcloud cs workers mycluster```

- get pods

```kubectl get pods```

- get services
  
```kubectl describe services```

- delete deployment
  
```kubectl delete deployment spring-app```

- delete services

```kubectl delete service spring-app```

