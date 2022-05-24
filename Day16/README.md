# Day 16: Run the docker container as a non-root user

Most of the Docker images use "root" as the default user. This can lead to a major security issue. To avoid running the docker container as root, we have to introduce a "non-root" user in the docker image. This can be achieved by  **USER** instruction in the dockerfile.

## Docker as a root user

If you check the running container using the 'id' command, the container shows its user and group. In the below execution, you can see the 'root' as the user and group.  

    # docker run  -it --rm debian:10.12-slim id
    uid=0(root) gid=0(root) groups=0(root)

## Create a non-root user in the dockerfile

To avoid the docker to run as a 'root' user, we have to include the 'non-root' user on the docker image. 

    FROM debian:10.12-slim
    RUN groupadd --gid 1000 navin \
        && useradd --uid 1000 --gid 1000 -m navin
    USER navin

In the first line of dockerfile, we are retrieving the 'Debian' as the base image. Then, create a user and group named 'navin' with the id of '1000'. Once the user and group creation is done, we are instructing the docker to run as user 'navin' which is a non-root user.

## Build the docker image and Verify

Let's build and run the image, then verify whether the container is running a root user or a non-root user.

    # docker build -t debian_non_root:v2 .

    # docker run -it --rm --name non_root_container debian_non_root:v2 id
    uid=1000(navin) gid=1000(navin) groups=1000(navin)

We can see that the container is using a non-root user that is specified in the dockerfile.