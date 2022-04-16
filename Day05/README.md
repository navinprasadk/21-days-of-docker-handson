# Day 05: Persist the container data with docker volumes

By default, the changes we made inside the container are lost if the container stops running. To store the files on the host machine, either we have to use docker **volume or bind mount**. Using this option, we can access the data even after the container gets deleted or restarted.

In this post, we will see how to persist the data using docker volumes.

## Docker volume

Volumes are stored in the host file system and it is managed by docker. Volumes is the preferred way to persist the data, it can be shared among the containers and it has a longer lifecycle than containers.

## Create a volume

    # docker volume create handson_data
    handson_data

Here handson_data is the name of the volume. If the name is not mentioned, docker generates a random name for the volume. By default, Docker uses the 'local' as the volume driver.

We can list the volume by *docker volume ls* command.

    # docker volume ls
    DRIVER    VOLUME NAME
    local     handson_data

## Inspect the volume

To know the details about the created volume, use the *docker inspect* command. This command gives the information about the storage driver, mount point and creation time.

    # docker volume inspect handson_data
    [
        {
            "CreatedAt": "2022-04-16T07:54:24Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "/var/lib/docker/volumes/handson_data/_data",
            "Name": "handson_data",
            "Options": {},
            "Scope": "local"
        }
    ]

## Remove the volume

    # docker volume remove handson_data
    handson_data

## Attach the volume to the container (Mounting)

To mount a volume or attach a volume to the container, either we can use the --volume or --mount option. Here, we see the --mount option to attach the volume to the container.

**Syntax to attach volume to the container:**  
docker run --mount source=[volume_name],destination=[path_in_container] [docker_image]

Let's run the Nginx container and mount the '/app' directory to the 'handson_data' volume. If the volume is not created, it will be created during the command execution

    # docker run --detach --name nginx_container --mount source=handson_data,target=/app nginx:1.21-alpine

The container has been started. Let's inspect the container and confirm whether the volume is attached or not.

    # docker inspect nginx_container
    ....
    ....
    "Mounts": [
        {
            "Type": "volume",
            "Name": "handson_data",
            "Source": "/var/lib/docker/volumes/handson_data/_data",
            "Destination": "/app",
            "Driver": "local",
            "Mode": "z",
            "RW": true,
            "Propagation": ""
        }
    ],
    ....
    ....

Look at the source and destination parameters in the above output, it matches with our docker volume path.

By this configuration, whatever changes you do in the container will reflect in the host file system. If the container gets deleted or restarted, we can recover the existing data from the host file system.