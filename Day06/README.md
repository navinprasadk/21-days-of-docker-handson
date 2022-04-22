# Day 06: Persist the container data with docker bind mount

## Bind mount

Bind mount stores file/directory anywhere on the host machine and it is mounted into the container. File/directory is referenced by absolute path on the host machine. If the file/directory doesn't exist on the host machine, docker automatically creates it. Compared to docker volumes, bind mount has limited functionalities. 

## Difference between --volume and --mount

- If you use **-v** or **--volume** to bind-mount a file/directory that does not yet exist on the Docker host, -v creates the endpoint for you. It is always created as a directory.
- If you use **--mount** to bind-mount a file or directory that does not yet exist on the Docker host, Docker does not automatically create it for you, but generates an error.

## Bind mount to the container

Here we are going to mount the directory **C:\workspace\docker\build** on the host machine to the **/app** on the container using the --mount paramater.

**Syntax to bind mount the container:**  
docker run --mount type=bind,source=[path_in_host],destination=[path_in_container] [docker_image]


Before executing the docker run command with --mount paramater, make sure that the **C:\workspace\docker\build** directory is present on the host. Otherwise, we will end up with the error. 

In docker for windows, if the file/directory is not there, docker is creating the file/directory. Ideally, it should not create as per the docker documentation(https://github.com/docker/for-win/issues/11958)

Let's run the container with bind mount,

    docker run --detach --name nginx_container --mount type=bind,source=C:\workspace\docker\build,target=/app nginx:1.21-alpine

## Inspect the bind mount

    # docker inspect nginx_container
    ....
    ....
    "Mounts": [
        {
            "Type": "bind",
            "Source": "C:\\workspace\\docker\\build",
            "Destination": "/app",
            "Mode": "",
            "RW": true,
            "Propagation": "rprivate"
        }
    ],
    ....
    ....

The directory C:\\workspace\\docker\\build is mounted to the /app on the container. Now, whatever changes you do in the /app directory gets refelcted in the C:\\workspace\\docker\\build on the container. Let's verfiy the same by creating a directory inside the container.

## Verify the bind mount

To create a directory inside the container, we need the shell access of container. Let's use the **docker exec** to get the shell access

    # docker exec -it nginx_container sh

Move to the /app directory and create a directpry named 'navin'

    # cd /app && mkdir navin

List the file/directory inside the /app directory

    # ls
    navin

Only the directory named 'navin' is present on the container. Let's check the host machine.

    # dir
    
    Directory of C:\\workspace\\docker\\build

    04/22/2022  04:51 PM    <DIR>          .
    04/22/2022  04:51 PM    <DIR>          ..
    04/22/2022  04:51 PM    <DIR>          navin
                0 File(s)              0 bytes
                3 Dir(s)  364,660,867,072 bytes free

Verified! The directory created on the container gets reflected on the host machine.
