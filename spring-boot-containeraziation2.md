
## Download

### clone project 
1. ```git clone```
2. mvn clean install to get target/
3. edit pom.xml add:
   ```<finalName>docker-spring-boot</finalName>```
   in build to make the jar name easier

4. Create the dockerfile

1. go to docker hub, identify what images you need
2. write docker file
```
FROM openjdk:8
ADD target/docker-spring-boot.jar docker-spring-boot.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar","docker-spring-boot.jar"]
```
- ADD add the built file to the Container, ADD <from> <to>, "<from>" is your target folder after ```maven clean install``` and <to> is your destination in your container. So basicalli the same arguments as ```mv <from> <to>``` which can also be used for renaming or moving

- EXPOSE expose the port 8080
- run the command to run the jar. ```java -jar ./target/docker-spring-boot.jar```  and you need to execute it in the container

```
  147  git add *
  148  git add * -f
  149  git commit -m 'added dockerfile and built the project'
  150  git config --global user.email "jeffpascal96@gmail.com"
  151  git config --global user.name "Jeff"
  152  git commit -m 'added dockerfile and built the project'
  153  git push
  154  history
```

### Build the docker file

1. ```docker build -f Dockerfile -t spring-app .```
- -f <target dockerfile>
- -t target name for app
- ```.``` location of the Dockerfile  

2. docker images