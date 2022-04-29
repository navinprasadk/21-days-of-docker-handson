# Day 10: Copy files between docker host and container

There are multiple ways to copy the files and directories between the docker host and containers. The most popular ones are

1. By mounting the host directory
2. Using the docker cp command
3. Using COPY instructions in dockerfile

Let's see each of the methods below.

## 1. By mounting the host directory

Here we are using [bind mount concept discussed in Day 06 post](../Day06/README.md) to sync the files between docker host and container. This is the preferred way.

    docker run --detach --mount type=bind,source=C:\workspace\docker\app,target=/app --name con_A debian:10.12-slim sleep infinity

    "sleep infinity" is the parameter passed here to run the container until we stop it
    Here the directory "C:\workspace\docker\app" on the docker host is shared on the container in the "/app" location

whatever files/directories are created in the docker host gets reflected on the container and vice versa.

The advantage of using this method is we can create a single shared directory on the Docker host for all the containers.

## 2. Using the docker cp command

Let's run a container-based out of Debian image with the "sleep infinity" parameter to run the container indefinitely.

    docker run --detach --name con_B debian:10.12-slim sleep infinity

In the next steps, we will copy the container from docker host to container and vice versa.

### copy from docker host to container

Let's assume, the file named "hostfile.md" from the docker host needs to be copied into a container named "con_B". We will see how to do it.

First, create a file named "hostfile.md" in the docker host and execute the **docker cp** command 

    docker cp hostfile.md con_B:/

After the successful execution of the above command. Get the shell access into the container named "con_B" and check whether the file is located or not.

### copy from container to the docker host

First, get the shell access into the container named "con_B" and then create a file named "con_B.md".

    docker exec -it con_B sh
    
    touch /con_B.md

Once the file is created, exit from the container "con_B" and jump into the docker host. Now, execute the **docker cp** command to copy the file from the container named "con_B" to the docker host

    docker cp con_B:/con_B.md C:\workspace\docker\con_B\con_B.md

Copied the file con_B.md successfully.

## 3. Using COPY instructions in dockerfile

COPY instruction in dockerfile is used to copy the file from docker host to container. And the file is included in the container which is created from that docker image.

Let's take the example of the below dockerfile. Here, we are copying all the files in the "src" directory from the docker host to the "/app" directory on the container. The file "index.html" and "Dockerfile" will be included in all the container which is created from that image.

    FROM debian:10.12-slim
    LABEL maintainer="navinprasadk"
    COPY . /app 
    CMD ["/bin/sh", "-c", "tail -f /dev/null"] 

Create the Docker image by building the above dockerfile

    docker build -t debian_custom:v1 .

And then run the container by using the above-created docker image

    docker run --detach --name con_c debian_custom:v1

Now, let's check the "/app" directory inside the container,

    docker exec -it con_c sh
    # cd /app
    # ls
    Dockerfile  index.html

That's done!

Each of the above copy methods has some advantages and disadvantages. We have to choose the method based on our needs.