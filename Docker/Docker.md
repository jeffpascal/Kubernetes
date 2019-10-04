# Docker

## Running a container

```docker container run -t ubuntu top```

### Inspect the container

This command allows you to enter a running container's namespaces with a new process.

```docker container exec```

### Get all containers

```docker container ls```

### Run bash inside a container

```docker container exec -it b3ad2a23fab3 bash```

- use exit to exit the container

PID is just one of the Linux namespaces that provides containers with isolation to system resources. Other Linux namespaces include:

MNT: Mount and unmount directories without affecting other namespaces.

NET: Containers have their own network stack.

IPC: Isolated interprocess communication mechanisms such as message queues.

User: Isolated view of users on the system.

UTC: Set hostname and domain name per container.

- Namespaces are a feature of the Linux kernel. However, Docker allows you to run containers on Windows and Mac. The secret is that embedded in the Docker product is a Linux subsystem. Docker open-sourced this Linux subsystem to a new project: LinuxKit. Being able to run containers on many different platforms is one advantage of using the Docker tooling with containers.

## Running multiple containers

### Run an NGINX server by using the official [NGINX image](https://store.docker.com/images/nginx) from the Docker Store:

```docker container run --detach --publish 8080:80 --name nginx nginx```

- The **--detach** flag will run this container in the background. 
- The **publish** flag publishes port 80 in the container (the default port for NGINX) by using port 8080 on your host.
    - The --publish flag is a feature that can expose networking through the container onto the host.
- You are also specifying the --name flag, which names the container. Every container has a name. If you don't specify one, Docker will randomly assign one for you.

```docker container inspect nginx```

### Run a MongoDB server. You will use the official MongoDB image from the Docker Store. Instead of using the latest tag (which is the default if no tag is specified), use a specific version of the Mongo image: 3.4.

```docker container run --detach --publish 8081:27017 --name mongo mongo:3.4```

