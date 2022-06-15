# Day 17: Monitor the resource usage of docker container

In this post, we will monitor the resource usage by docker container using docker stats.  

## Docker stats  

Docker stats command is used to display the container(s) resource usage. It displays CPU percentage, Memory Usage/Limit, Memory percentage, Network I/O, Block I/O and PIDs.

Before playing with the docker stats command, I'm going to run the two containers in my docker host.

    # docker stats
    CONTAINER ID   NAME              CPU %     MEM USAGE / LIMIT     MEM %     NET I/O     BLOCK I/O   PIDS
    6d7b6f0bc092   bold_moore        0.00%     8.77MiB / 24.94GiB    0.03%     976B / 0B   0B / 0B     9
    091e2825cd59   gifted_ishizaka   0.00%     1.098MiB / 24.94GiB   0.00%     586B / 0B   0B / 0B     1

By default, docker stats command displays the resource usage statistics of all the running containers in the host. If you want to see the statistics of specific container, add the container name or id in the docker stats command.

    # docker stats 6d7b6f0bc092
    CONTAINER ID   NAME         CPU %     MEM USAGE / LIMIT    MEM %     NET I/O     BLOCK I/O   PIDS
    6d7b6f0bc092   bold_moore   0.00%     8.77MiB / 24.94GiB   0.03%     976B / 0B   0B / 0B     9

If you want to see the resource usage statistics of running container as well as idle container, run **docker stats -all**  

    # docker stats --all 
    CONTAINER ID   NAME                       CPU %     MEM USAGE / LIMIT     MEM %     NET I/O       BLOCK I/O   PIDS
    091e2825cd59   gifted_ishizaka            0.00%     1.098MiB / 24.94GiB   0.00%     796B / 0B     0B / 0B     1
    eb9388e351ef   stupefied_kowalevski       0.00%     0B / 0B               0.00%     0B / 0B       0B / 0B     0
    6d7b6f0bc092   bold_moore                 0.00%     8.77MiB / 24.94GiB    0.03%     1.05kB / 0B   0B / 0B     9
    f80beaab3a0f   upbeat_blackburn           0.00%     0B / 0B               0.00%     0B / 0B       0B / 0B     0
    58a2c1ac6e22   compassionate_gates        0.00%     0B / 0B               0.00%     0B / 0B       0B / 0B     0
    bfe989ce6615   interesting_pasteur        0.00%     0B / 0B               0.00%     0B / 0B       0B / 0B     0
    1453f01b857e   webapp                     0.00%     0B / 0B               0.00%     0B / 0B       0B / 0B     0

