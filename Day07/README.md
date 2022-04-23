# Day 07: Attach a container to the Docker network

## Docker network introduction

Docker network allows containers to communicate with other containers, and also communicate with the outside world. Docker supports networking for its containers through the network drivers.

### Network drivers

The network drivers used in the docker are below,

- Bridge  
Default network type  
Whenever the docker gets started, a bridge network gets created and the newly created containers are automatically connected to the bridge network  
Used to communicate with containers running on the same docker host  
Containers running in the same bridge network can communicate with each other  
For ex: Let's say two containers 'A' and 'B' are connected to the same bridge network, both A and B can able to communicate with each other  

- Host  
Uses the network provided by the docker host machine  
By using the host network, there is no isolation between containers and host on this network  
For ex: If you run the Nginx container on port 80 with the host network, we can access the Nginx using the host IP address  i.e., <host IP address>:80  

- Overlay  
The overlay network is used to communicate with containers spread across multiple docker host and enables swarm services to communicate with each other  
For ex: if a container on the host machine named 'A' wants to communicate with the container on host machine 'B', we can use this network  

- None  
Disables all types of networking, it is not available for swarm services. If a user wants to disable the networking access, we can use it. Once the containers get added to this network, they can't able to communicate with containers and the outside world.

- Macvlan  
Macvlan networks allow you to assign a MAC address to a container, making it appear as a physical device on your network  
The Docker daemon routes traffic to containers by their MAC addresses  
Using the macvlan driver is sometimes the best choice when dealing with legacy applications that expect to be directly connected to the physical network, rather than routed through the Docker host’s network stack  
Macvlan networks are best when you are migrating from a VM setup or need your containers to look like physical hosts on your network, each with a unique MAC address  

Note: A container can be linked to many networks

## Create a network

To create a network, use docker network command,

    # docker network create test-network
    7fd521863efb14420ab77a3b66261ad3ee040fca2b45b1a5e3ca7cb51a0723d6

The network has been created with the default option. If required we can pass the driver details, subnet range etc.,

## List a network

List the networks to check if 'test-network' is present

    # docker network ls
    NETWORK ID     NAME           DRIVER    SCOPE
    072220e85413   bridge         bridge    local
    afa0ee9daab1   host           host      local
    ce679daeb0d3   none           null      local
    7fd521863efb   test-network   bridge    local

While creating a network, we didn't mention the network driver, it has automatically taken the bridge network driver

In the above network list, we can see there are two bridge networks. One is the default bridge network(name: bridge) and the other one is a user-defined bridge network(name: test-network). We will see the difference between them in the next post.

## Inspect a network

If you want to know about the subnet details, containers attached to the network and some parameters regarding the network, we can inspect it and find the details.

    # docker network inspect test-network
    [
        {
            "Name": "test-network",
            "Id": "7fd521863efb14420ab77a3b66261ad3ee040fca2b45b1a5e3ca7cb51a0723d6",
            "Created": "2022-04-23T14:48:57.9254641Z",
            "Scope": "local",
            "Driver": "bridge",
            "EnableIPv6": false,
            "IPAM": {
                "Driver": "default",
                "Options": {},
                "Config": [
                    {
                        "Subnet": "172.18.0.0/16",
                        "Gateway": "172.18.0.1"
                    }
                ]
            },
            "Internal": false,
            "Attachable": false,
            "Ingress": false,
            "ConfigFrom": {
                "Network": ""
            },
            "ConfigOnly": false,
            "Containers": {},
            "Options": {},
            "Labels": {}
        }
    ]
Currently, no containers are attached to the above network. That's why the container object is empty here.

## Connect a container to the network

Let's run the container first and then connect it to the existing 'test-network' network

    # docker run --detach --name nginx_container --mount  type=volume,source=handson_data,target=/app nginx:1.21-alpine
    c2b27b5f8ee44b2ca1cc23b49cb50fe2d621153e8eda277cba0e0f46c90edd10

Container has been successfully created. Use the **docker network connect** command to connect the container to the network

    # docker network connect test-network nginx_container

A container named 'nginx_container' has been connected to the 'test-network'. Let's inspect the network and confirm

    # docker network inspect test-network
    [
    {
        "Name": "test-network",
        "Id": "7fd521863efb14420ab77a3b66261ad3ee040fca2b45b1a5e3ca7cb51a0723d6",
        "Created": "2022-04-23T14:48:57.9254641Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "c2b27b5f8ee44b2ca1cc23b49cb50fe2d621153e8eda277cba0e0f46c90edd10": {
                "Name": "nginx_container",
                "EndpointID": "e55386f21d6548e54fabe326355c88d31cbb6c3352ecf59b8b8daba2cd53287c",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
    ]

In the containers section, we can see the 'nginx-container' details. That's the end of the post.
