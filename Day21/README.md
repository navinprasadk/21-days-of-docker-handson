# Day 21: Configure Docker logging drivers

When containerized application throws the logs, it has been sent to **std out** and **std err**. By using the docker logs command, we can see those logs from a running container. However, the docker logs command might not be useful if the docker sends the log to the file or database or centralised environment.  

According to docker's official documentation,

"In some cases, docker logs may not show useful information unless you take additional steps.

- If you use a logging driver which sends logs to a file, an external host, a database, or another logging back-end, and has “dual logging” disabled, docker logs may not show useful information.
- If your image runs a non-interactive process such as a web server or a database, that application may send its output to log files instead of STDOUT and STDERR.

In the first case, your logs are processed in other ways and you may choose not to use docker logs. In the second case, the official Nginx image shows one workaround, and the official Apache HTTPd image shows another.

The official Nginx image creates a symbolic link from /var/log/nginx/access.log to /dev/stdout, and creates another symbolic link from /var/log/nginx/error.log to /dev/stderr, overwriting the log files and causing logs to be sent to the relevant special device instead. See the Dockerfile.

The official httpd driver changes the httpd application’s configuration to write its normal output directly to /proc/self/fd/1 (which is STDOUT) and its errors to /proc/self/fd/2 (which is STDERR)"

## Logging drivers

Docker includes multiple logging mechanisms to get the logs from the running containers. These mechanisms are called logging drivers. By default, Docker uses **json-file** logging driver which stores container logs in the JSON format. There is a list of logging drivers that comes with docker you can use any of it. I'm listing the popular logging drivers, below,

| Driver   |    Description |
|-------   |-------------------|
| none     |  No logs are available for the container and Docker logs do not return any output.|
| local    |   Logs are stored in a custom format designed for minimal overhead.|
| json-file|    Logs are formatted as JSON. The default logging driver for Docker.|
| syslog   |    Writes logging messages to the Syslog facility. The Syslog daemon must be running on the host machine.|
| journald |    Writes log messages to journald. The journald daemon must be running on the host machine.|
| splunk   |        Writes log messages to Splunk using the HTTP Event Collector.|

In addition to using the logging drivers included with Docker, you can also implement and use logging driver plugins.

## Configure the logging driver for a container

There are two ways you can configure a logging driver. They are

- Configure the default logging driver for all containers
- Configure the logging driver for each container

### Configure the default logging driver for all containers

Here, we are going to set the default logging driver as 'local' by editing the daemon.json. Once it is configured, all the new containers in the host will start using the updated logging driver

    vi daemon.json 
    {
    "log-driver": "local"
    }

After editing the daemon.json, save and exit the file. Next, restart the docker to reflect the new value. Existing containers use the default logging driver, newly created containers use the updated logging driver 'local'.

    sudo systemctl restart docker

To find the current default logging driver for the Docker daemon, run docker info and search for Logging Driver. 

     docker info --format '{{.LoggingDriver}}'
    'local'

### Configure the logging driver for each container

Sometimes, we may require a logging driver other than the ones which are configured in the daemon.json. In that case, we have to define the logging driver in the docker run command.

The following example starts an Alpine container with the none logging driver.

    docker run -it --log-driver none alpine ash

List the container and check its name,

    docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
    71c40f585ae2   alpine    "ash"     29 seconds ago   Up 28 seconds             exciting_jones

Let's check the configured logging driver for this particular container,

    docker inspect -f '{{.HostConfig.LogConfig.Type}}' 71c
    'none'

Here, you can see that the above output shows as 'none'