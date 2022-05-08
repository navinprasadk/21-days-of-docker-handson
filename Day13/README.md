# Day 13: Pass environment variables to Docker containers

In this post, we will see the different ways to pass environment variables to the docker container.

- Using the ENV parameter in the docker run command
- Using the ENV file
- In DOCKERFILE
- In docker-compose

## 1. Using the ENV parameter in docker run command

We have seen that we can use the docker run command to run the containers. With the docker run command itself, we can pass the environment variables to containers. To do so, the --env parameter should be used.

The syntax is,

    docker run --env NAME=VALUE --name <container-name> <image-name>

To pass multiple environment variable, the syntax is,

    docker run --env NAME1=VALUE --env NAME2=VALUE --name <container-name> <image-name>

Let's see how to pass the env variables to the docker container named "dev" based on the Debian docker image

    docker run --detach --rm --env USER=navin --env MATURITY=dev --env SOURCE=docker-run --name dev debian:10.12-slim sleep 30
    eab0f4c3e7f420d03ecc55d1caf5b35b25f7ec0bff2cac299c134538a43d2bed

Now, the container has been created. Let's check if the environment variable is available inside the container or not by executing the **env** command.

    docker exec dev env
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    HOSTNAME=eab0f4c3e7f4
    TERM=xterm
    USER=navin
    MATURITY=dev
    SOURCE=docker-run
    HOME=/root

In the above result, we see that the variables USER, MATURITY and SOURCE are assigned with values passed from the docker run command.

## 2. Using the ENV file

To pass the environmental variable, --env parameter in the docker run command is simple to use. However, if you have a long list of variables to pass, using the --env parameter is not convenient and hard to manage. In that case, it is better to go with the env file where you can define any number of variables easily.  

Let's create a simple vars.env file and see how to pass it,

    # vars.env file
    # Docker environment variable
    SOURCE=env_file
    USER=navin
    MATURITY=staging

env file has been created with the name "vars.env". Next, we will include the vars.env file in the docker run command.

    docker run --detach --rm --env-file vars.env --name staging debian:10.12-slim sleep 30
    32fd408411d8769a16ec7c1d971ccac1dfa73f93c0cbc06b6c4bbebc599c728f

Verify whether the container has the variables or not.

    docker exec staging env
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    HOSTNAME=32fd408411d8
    TERM=xterm
    SOURCE=env_file
    USER=navin
    MATURITY=staging
    HOME=/root

We have verified that the container has the environment variables.

## 3. In DOCKERFILE

On the previous two occasions, we passed environment variables after the docker image is built i.e during the runtime. But here we are going to pass the environment variables before the docker image gets built.

Let's create a Dockerfile and add the ENV instructions, 

    FROM debian:10.12-slim
    LABEL maintainer="navinprasadk"
    ENV SOURCE=DOCKERFILE
    ENV maturity=pre-prod
    COPY . /app 
    CMD ["/bin/sh", "-c", "tail -f /dev/null"]

Once the Dockerfile is created, build the image using the docker build command,

    docker build -t debian_with_env:v1 .

Next, run the container and check whether the environment variable is present or not.

    docker run --detach --rm --name pre-prod debian_with_env:v1 sleep 30
    0e44dbda3b3667e46b6d5759ad1e4aade7bf3ed2ff031bc1eaa6b599560cdbb0

    docker exec pre-prod env
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    HOSTNAME=0e44dbda3b36
    TERM=xterm
    SOURCE=DOCKERFILE
    maturity=pre-prod
    HOME=/root

The env variables we defined in the dockerfile are available inside the container.

## 4. In docker-compose

In the docker-compose file, we can pass the environment variable as a .env file and variable. In this example, we will see how to pass, the env variable as a variable.

Here is an example of docker-compose.yml file where the environment variable is defined,

    # docker-compose.yml
    version: "3.0"
    services:
        debian_compose_env:
            image: debian:10.12-slim
            environment:
                USER: navin
                MATURITY: prod
                SOURCE: docker-compose
            command: tail -f /dev/null
            # Incase if your passing environment variables through .env file
            # env_file:
            #   - compose-vars.env     # path to  env file

Launch the containers from a docker-compose file using the "docker-compose up" command and then check the container whether the variables are present or not.

    docker-compose up -d
    [+] Running 2/2
    - Network src_default                 Created           0.1s
    - Container src-debian_compose_env-1  Started           0.6s

    docker-compose exec debian_compose_env env
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    HOSTNAME=5860eb647fb7
    TERM=xterm
    SOURCE=docker-compose
    USER=navin
    MATURITY=prod
    HOME=/root

Perfect! Variables are present in the container. We have seen different ways to pass the environment variables in the post, each method has some advantages and disadvantages. Based on the use case, we have to select the appropriate one. That's it for today's post. Bye Bye.  
