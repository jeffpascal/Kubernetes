# Dockerizing a full stack application (Back and Front)

The setup is:

One angular application that opens on port 4200 with a GET request on localhost:8080/userTime that shows a number in the title of the application

One spring-boot application with a endpoint /userTime running tomcat on port 8080


Step by step instruction to start develiping:

1. git clone https://github.com/jeffpascal/Spring-and-springboot/tree/testingcompose
2. cd font-app
3. npm install
4. cd SpringSecurity
5. mvn clean install
6. docker-compose up

## Writing Dockerfile for Spring Boot

```
FROM openjdk:8
WORKDIR /
ADD target/docker-spring-boot.jar docker-spring-boot.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar","docker-spring-boot.jar"]
```

1. FROM JAVA 8
2. Specify workdir as root folder
3. Expose 8080 port
4. Run the java application in the standard way java -jar docker-spring-boot.jar

### Prerequisites:

- Build the application before running the docker-compose.

### TODO: 

- Make the building local to the container
- Make hot reloading work without remote debugging/building

## Writing Dockerfile for Angular

Hot reloading works perfectly fine because I added a volume from my local env to the container.

```
FROM node:14-slim
RUN npm install @angular/cli@latest -g
RUN mkdir -p /app
WORKDIR /app
EXPOSE 4200
CMD ng serve --port 4200 --host 0.0.0.0 --poll 1
```

### Prerequisites

- npm install in your local env

### TODO

- Move npm install step to the container


## Writing Docker Compose for developement environement

This docker-compose has 2 services: front and back

1. The context of the application is in the folders of the applications in your local environement

2. Specify the dockerfile for each of them

3. For hot-reloading for angular, i linked my local copy of the project to the app folder created in the dockerfile

4. Expose the port 4200 that the serve command shows.

5. back service is the spring-boot application

```
version: "3"
services:
  front:
    build:
      context: ./font-app
      dockerfile: Dockerfile
    volumes:
      - ./font-app:/app
    ports:
      - 4200:4200 
  back:
    build:
      context: ./SpringSecurity
      dockerfile: Dockerfile
    ports:
      - 8080:8080
```