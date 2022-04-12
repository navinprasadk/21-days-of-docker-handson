# Day 03: Push the docker image to the container registry

Welcome you all to the third post of the docker hands-on series. Today, we will see how to publish the docker images to the container registry.

## Container Registry

- **Container registry** is the place to store and distribute the images. A registry is organised into multiple repositories, repository contains multiple versions of images
- By default, docker interacts with **docker hub**. Dockerhub is the docker registry hosted by Docker
- If required, You can run your version of the Docker registry on an on-premise server. For more info https://docs.docker.com/registry/

## Push the docker image to the docker hub

In the previous post, we built a docker image named webserver:v1. Now, let's push the same image to the docker hub. 
To know the registry URL, type the docker info command,

    # docker info
    .....
    No Proxy: hubproxy.docker.internal
    Registry: https://index.docker.io/v1/
    Labels:
    Experimental: false
    .....

## Login with the docker registry

Copy the registry URL from the above output. Log in with the docker registry using docker hub account credentials. If you don't have an account, create it here https://hub.docker.com/

    # docker login https://index.docker.io/v1/ -u <username> -p <password>
    WARNING! Using --password via the CLI is insecure. Use --password-stdin.
    Login Succeeded 

## Tag the docker image
Next step is tag the docker image whoch we are going to push.

Syntax: docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

    # docker tag webserver:v1 xxxx/webserver:v1

Here the xxxx denotes the user id from the docker hub.
webserver denotes the respoitory name and the v1 refers to the ersion of the docker image

## Push the docker image

Docker push is the command used to push an image or a repository to a registry

Syntax:  docker push [OPTIONS] NAME[:TAG]

    # docker push xxxx/webserver:v1
    The push refers to repository [docker.io/xxxx/webserver]
    a795e088a51e: Pushed
    b991c80c3ef2: Pushed
    8df6b63c60d4: Pushed
    d63b53686463: Pushed
    c0b09410617a: Pushed
    be9057e6dae4: Pushed
    4fc242d58285: Pushed
    v1: digest: sha256:4aeb0ddf88b5061ea5fe1faddf1c6794c5fa64825698316913e7462ae0d9cc8a size: 1775

Great! we have successfully uploaded to the registry! Now if anybody needs your docker image, they can download it from dockerhub using the following command **docker.io/<username>/webserver**
