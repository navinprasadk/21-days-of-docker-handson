# Day 11: CMD and ENTRYPOINT instructions in the dockerfile

There are two instructions in dockerfile which defines the processes running inside the container. They are

- ENTRYPOINT
- CMD

## ENTRYPOINT

Setting the ENTRYPOINT instruction in a Dockerfile instructs Docker to run a specific command when the container starts.

Syntax: ENTRYPOINT ["executable", "param1", "param2"] (or) ENTRYPOINT command param1 param2  

The program or script or executable that needs to run, should be mentioned in the ENTRYPOINT instruction. The value/parameter required for the specific program/executable we can pass through the docker command line.

    FROM debian:10.12-slim
    ENTRYPOINT sleep

When you run the container, based on the above docker image, the container executes the sleep program and then stops it.  

Create the Docker image using the above dockerfile  

    docker build -t debian_sleep:v1 .

And then run the container by using the above-created docker image. When you use ENTRYPOINT, in the docker command line you don't need to mention the program/executable  name, just pass the value/parameter, it gets appended to the ENTRYPOINT instruction. In the case of CMD instruction, the entire program/executable and value/parameter get replaced.  

    docker run --detach --name sleeper debian_sleep:v1 25

Here, the container sleeper runs for 25 seconds and then stops.  

There is a way to override the ENTRYPOINT instruction, we have to add **--entrypoint** in the docker command line before the "--name".  

For example: 

    docker run --detach --entrypoint sleep2.0 --name sleeper debian_sleep:v1 25

## CMD

CMD is short for command and provides the default value for an executing container. If an executable is not specified in the CMD instruction, then ENTRYPOINT must be specified as well. There can only be one CMD instruction in a Dockerfile.  

Syntax: CMD ["command","param1"]  

If you pass CMD and the docker command line parameters, the latter overrides the former entirely  

    FROM debian:10.12-slim
    CMD sleep 10

In the above dockerfile, look at the CMD instruction, we can see that there are two things mentioned **sleep 10** where sleep is the program/executable name and 10 is the value/parameter passing to the program/executable. Let's build the image and run the container using the following commands,

    docker build -t debian_sleep:v2 .
    docker run --detach --name sleeper debian_sleep:v2

The container named "sleeper" keeps running for 10 seconds and then it goes down. The 10 seconds sleep duration is permanent Since it is mentioned in the dockerfile.  

Suppose you want to update the sleep duration from 10 to 20, how will you do that?  
From the docker command line, we can update the CMD instruction by adding the name of the program/executable and value/parameter,

    docker run --detach --name sleeper debian_sleep:v2 sleep 20

This time container runs for 20 seconds because the docker command line parameter overrides the CMD instruction in dockerfile.  

## ENTRYPOINT and CMD

We can include ENTRYPOINT and CMD together in the same dockerfile. Using CMD, we can pass the default value/parameter to the program/executable mentioned in the ENTRYPOINT

    FROM debian:10.12-slim
    ENTRYPOINT sleep 
    CMD 50

This time if we run the container based on the above dockerfile, we don't need to pass the value/parameter for the sleep program because the default value is mentioned in the CMD instruction. If we pass the value/paramter, it overides the CMD in the dockerfile  