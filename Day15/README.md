# Day 15: Check the container's health using HEALTHCHECK instruction

## Health check

Health check in docker helps to check whether the docker container is healthy or not. Docker performs the health check at regular intervals.  

## HEALTHCHECK instruction  

The syntax for HEALTHCHECK instruction is,
    HEALTHCHECK [options] CMD \<command>

The available options are,

    --interval=DURATION (default 30s)
    --timeout=DURATION (default 30s)
    --start-period=DURATION (default 30s)
    --retries=N (default 3)

When a container has a HEALTHCHECK instruction, it has a health status in addition to its normal status. This status is initially **starting.** Whenever a health check passes, it becomes **healthy** (whatever state it was previously in). After a certain number of consecutive failures, it becomes **unhealthy**

## Add HEALTHCHECK to the dockerfile

We can perform the health check by including the HEALTHCHECK instruction in the dockerfile. Let's write a simple dockerfile which is based on the Nginx docker image and perform the health check.

    # Dockerfile
    FROM nginx:alpine
    HEALTHCHECK --interval=25s --timeout=5s CMD curl --fail http://localhost:80/ || exit 1
    EXPOSE 80

Here we have set the configuration in such a way that docker has to check the container status every 25 seconds, if the container doesn't respond for 5 seconds, the docker will mark the container as **unhealthy**. Otherwise, it will mark the container as **healthy**

Let's build the container and run it,

    # docker build -t nginx_hc:v1 .  

    # docker run --name webapp_hc --publish 80:80 --detach nginx_hc:v1

Now, list the container and see the status of it,

    # docker ps
    CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS                            PORTS                NAMES
    4f018044cb35   nginx_hc:v1   "/docker-entrypoint.…"   3 seconds ago   Up 4 seconds (health: starting)   0.0.0.0:80->80/tcp   webapp_hc

In the above response, under the status field, you can see the health status of the docker container. Initially, it is shown as "starting". If you check after some time, it will either show the status as healthy or unhealthy.

    # docker ps
    CONTAINER ID   IMAGE         COMMAND                  CREATED        STATUS                  PORTS                NAMES
    4f018044cb35   nginx_hc:v1   "/docker-entrypoint.…"   20 minutes ago   Up 20 minutes (healthy)   0.0.0.0:80->80/tcp   webapp_hc

Now, it is showing as **healthy**. This is how we can perform the health check using the HEALTHCHECK instruction in docker.  
