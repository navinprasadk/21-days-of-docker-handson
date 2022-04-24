# Day 08: Communication between two containers using a docker bridge network

In this post, we will see how we communicate between two docker containers on the bridge network. There are two bridge networks available. One is the default bridge network which gets created when the docker gets started. Another one is a user-defined bridge network, as the name suggests, it is created by the user.

## Difference between default and user-defined bridge network

1. On the default bridge network, containers can only access each other by IP addresses, unless you use the --link option, which is considered legacy. On a user-defined bridge network, containers can resolve each other by name
2. Containers without --network specified, attached to the default bridge network. It leads to risk, unrelated containers get the opportunity to communicate. On the other hand, a User-defined  bridge network is a scoped one, meaning that only containers attached to the network can communicate
3. To remove the running container from the default bridge network, the container should be stopped and recreated with different options. On the user-defined network, we can remove/connect it on the go.
4. On the default bridge network, suppose your container requires some modification in the network configs, we can modify it and requires docker restart. But the problem is all the containers use the same settings such as MTU and Iptables. On the user-defined bridge, if the different groups of applications have different network requirements, we can configure each user-defined bridge separately

## Communicate between two containers in the default bridge network

The default bridge network is already created by docker itself. In this section, we will create the containers and verify the communication using ping command.

### Run the containers and Verify the connection between them

Let's run the two containers named container_A based out of Nginx image and container_B based out of busybox image and try to ping it from container_B to container_A.

    # docker run --detach --name container_A nginx:1.21-alpine

A container has been created. Let's inspect the default bridge network and see the container_A is automatically added or not.

    # docker network inspect bridge

If you check the sections of the container from the output of the docker inspect command, container_A will be listed as below,

    ....
    ....
    "Containers": {
        "30303d23846eb2cf67ad1686e37052053a8c3d099430628f54d35f507794b0d3": {
            "Name": "container_A",
            "EndpointID": "3d49410bcaf4008a0a1f47cec1ee24c2a2e9ba87c0514143029b829d3688775e",
            "MacAddress": "02:42:ac:11:00:02",
            "IPv4Address": "172.17.0.2/16",
            "IPv6Address": ""
        }
    },
    ....
    ....

We can find the IP address of the container_A by inspecting it

    # docker inspect container_A
    ....
    ....
    "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "072220e85413aced0064fb814d2938198fe185a0881e772034979fac93e692e4",
                    "EndpointID": "3d49410bcaf4008a0a1f47cec1ee24c2a2e9ba87c0514143029b829d3688775e",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
    ....
    ....

Here, the container_A's IP is 172.17.0.2. Let's run another container named container_B and try to ping container_A.

    # docker run -it --name container_B busybox:1 sh
    / # ping 172.17.0.2
    PING 172.17.0.2 (172.17.0.2): 56 data bytes
    64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.173 ms
    64 bytes from 172.17.0.2: seq=1 ttl=64 time=0.064 ms
    64 bytes from 172.17.0.2: seq=2 ttl=64 time=0.117 ms
    64 bytes from 172.17.0.2: seq=3 ttl=64 time=0.047 ms
    64 bytes from 172.17.0.2: seq=4 ttl=64 time=0.062 ms
    64 bytes from 172.17.0.2: seq=5 ttl=64 time=0.058 ms
    64 bytes from 172.17.0.2: seq=6 ttl=64 time=0.058 ms
    64 bytes from 172.17.0.2: seq=7 ttl=64 time=0.091 ms
    64 bytes from 172.17.0.2: seq=8 ttl=64 time=0.065 ms
    64 bytes from 172.17.0.2: seq=9 ttl=64 time=0.090 ms
    64 bytes from 172.17.0.2: seq=10 ttl=64 time=0.113 ms
    ^C
    --- 172.17.0.2 ping statistics ---
    11 packets transmitted, 11 packets received, 0% packet loss
    round-trip min/avg/max = 0.047/0.085/0.173 ms

Ping works! Now, we've verified that the ping from B to A works in the default bridge network

## Communicate between two containers in the user-defined bridge network

In this section, create a new network of type bridge, then create two new containers and attach them to the newly created user-defined network.

### Create a user-defined bridge network

    # docker network create --driver bridge handson_net
    2947cbd1e4bb0183b09af9c6f6f3e95bbbdbd0a95e99048f363521b14d5cc090

    # docker network ls
    NETWORK ID     NAME           DRIVER    SCOPE
    072220e85413   bridge         bridge    local
    2947cbd1e4bb   handson_net    bridge    local
    afa0ee9daab1   host           host      local
    ce679daeb0d3   none           null      local
    7fd521863efb   test-network   bridge    local

A new network named 'handson_net' of 'bridge' driver has been created.

### Run the containers and attach them to the user-defined bridge network

Let's run the container named container_C based on the Nginx image and attach it to the handson_net bridge network

    # docker run --detach --name container_C --network handson_net nginx:1.21-alpine

A container has been created. Let's inspect the user-defined bridge network and see the container_C is added or not.

    # docker network inspect handson_net

If you check the sections of the container from the output of the docker inspect command, container_C will be listed as below,

    ....
    ....
    "ConfigOnly": false,
    "Containers": {
        "19e799fd9df62c4f379cdcd8c185932684fde913b0552fdfd92bac1862c13b77": {
            "Name": "container_C",
            "EndpointID": "06374cc044189efcac2ba7d76b83f1d9b8dda7f1c1810a0904cc922b72608c36",
            "MacAddress": "02:42:ac:13:00:02",
            "IPv4Address": "172.19.0.2/16",
            "IPv6Address": ""
        }
    },
    ....
    ....

We can find the IP address of the container_C by inspecting it

    # docker inspect container_C
    ....
    ....
    "Networks": {
            "handson_net": {
                "IPAMConfig": null,
                "Links": null,
                "Aliases": [
                    "19e799fd9df6"
                ],
                "NetworkID": "2947cbd1e4bb0183b09af9c6f6f3e95bbbdbd0a95e99048f363521b14d5cc090",
                "EndpointID": "06374cc044189efcac2ba7d76b83f1d9b8dda7f1c1810a0904cc922b72608c36",
                "Gateway": "172.19.0.1",
                "IPAddress": "172.19.0.2",
                "IPPrefixLen": 16,
                "IPv6Gateway": "",
                "GlobalIPv6Address": "",
                "GlobalIPv6PrefixLen": 0,
                "MacAddress": "02:42:ac:13:00:02",
                "DriverOpts": null
            }
        }
    ....
    ....

Here, the container_C's IP is 172.19.0.2.

### Verify the connection

Let's run another container named container_D based on the busybox image and try to ping container_C (without attaching it to any network).

    # docker run -it --name container_D busybox:1 sh
    / # ping 172.19.0.2
    PING 172.19.0.2 (172.19.0.2): 56 data bytes
    ^C
    --- 172.19.0.2 ping statistics ---
    15 packets transmitted, 0 packets received, 100% packet loss

Ping failed.  
While creating container_D, it gets added to the default bridge network but the container_C is present in the user-defined bridge network. That's why the ping connection is failing. Let's add container_D to the user-defined bridge network and check the results.

    # docker run -it --name container_D --network handson_net busybox:1 sh
    / # ping 172.19.0.2
    PING 172.19.0.2 (172.19.0.2): 56 data bytes
    64 bytes from 172.19.0.2: seq=0 ttl=64 time=0.151 ms
    64 bytes from 172.19.0.2: seq=1 ttl=64 time=0.057 ms
    64 bytes from 172.19.0.2: seq=2 ttl=64 time=0.076 ms
    64 bytes from 172.19.0.2: seq=3 ttl=64 time=0.066 ms
    64 bytes from 172.19.0.2: seq=4 ttl=64 time=0.063 ms
    ^C
    --- 172.19.0.2 ping statistics ---
    5 packets transmitted, 5 packets received, 0% packet loss
    round-trip min/avg/max = 0.057/0.082/0.151 ms

Ping works! From this experiment, we can understand that the containers in the same bridge network can easily communicate with each other.