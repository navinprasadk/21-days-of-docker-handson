# Day 12: Run multiple containers using docker-compose

Till now, we have been working with single containers. But in many cases, we have to run multiple containers. To run the multiple containers, if we use the docker run command, there are some shortcomings such as it would be difficult to remember all the parameters used for the docker run command and we couldn't launch multiple containers at the same time.

## What docker-compose?

According to docker's official documentation,

    "Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration."

For instance, if you want to run Nginx and a web application, you create a YAML file where you can define your service. By using the YAML file, you can launch the containers.

## Why docker-compose?

With docker-compose, you can define and launch multiple containers at the same time using a single YAML file. Also, you can define all the docker components such as docker volume and network in the YAML file.

In this post, we will see how to run sonarqube using docker-compose. To run sonarqube, here we are using two containers, one is sonarqube and the other one is the Postgres database.

## How to use docker-compose?

According to docker's official documentation,

Using Compose is a three-step process,

1. Define your app’s environment with a Dockerfile so it can be reproduced anywhere.
2. Define the services that make up your app in docker-compose.yml so they can be run together in an isolated environment.
3. Run docker compose up and the Docker compose command starts and runs your entire app. You can alternatively run docker-compose up using the docker-compose binary.

For us, the first step (defining the app environment in dockerfile) is not needed. Because the sonarqube application is built by the sonarqube team and the docker image is readily available. We can consume the docker image.

Let's move to the second step. (creating a docker-compose)

    # docker-compose.yml
    version: "3.0"
    services:
    sonarqube:
        image: sonarqube:9.4.0-developer
        depends_on:
        - db
        environment:
            SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
            SONAR_JDBC_USERNAME: sonar
            SONAR_JDBC_PASSWORD: sonar
        volumes:
        - sonarqube_data:/opt/sonarqube/data
        - sonarqube_extensions:/opt/sonarqube/extensions
        - sonarqube_logs:/opt/sonarqube/logs
        ports:
        - "9000:9000"    
    db:
        image: postgres:12
        environment:
            POSTGRES_USER: sonar
            POSTGRES_PASSWORD: sonar
        volumes:
        - postgresql:/var/lib/postgresql
        - postgresql_data:/var/lib/postgresql/data

    volumes:
    sonarqube_data: {}
    sonarqube_extensions: {}
    sonarqube_logs: {}
    postgresql: {}
    postgresql_data: {}

Here, we defined two containers one for the sonarqube server and the other one for is Postgres database. In addition to that, we have the network and volume required for those two containers. 

To communicate sonarqube with the Postgres database, we defined some parameters as environment variables in the sonarqube container.

Finally, launch the containers using docker compose up,

    docker-compose up -d

    [+] Running 18/18
    - db Pulled                                                                                       44.7s
    - 1fe172e4850f Pull complete                                                                      15.0s
    - c2bb685f623f Pull complete                                                                      15.3s
    - 3027ff705410 Pull complete                                                                      15.4s
    - 062371e3461d Pull complete                                                                      15.5s
    - 39d54e944de7 Pull complete                                                                      16.2s
    - 6530357dda9a Pull complete                                                                      16.3s
    - b1d302dc78c6 Pull complete                                                                      16.4s
    - f6d91cb1d3c1 Pull complete                                                                      16.5s
    - 85c75cecd287 Pull complete                                                                      40.7s
    - ce327230a313 Pull complete                                                                      40.9s
    - 55277580dd99 Pull complete                                                                      41.0s
    - 2f48dddf94f1 Pull complete                                                                      41.2s
    - 1f949a245982 Pull complete                                                                      41.3s
    - sonarqube Pulled                                                                                69.0s
    - 97518928ae5f Pull complete                                                                      10.5s
    - 7906ea535067 Pull complete                                                                      63.3s
    - 675dfa4d1dcf Pull complete                                                                      63.4s
    [+] Running 8/8
    - Network src_default Created                                                                     0.1s
    - Volume "src_postgresql_data"       Created                                                      0.0s
    - Volume "src_sonarqube_data"        Created                                                      0.0s
    - Volume "src_sonarqube_extensions"  Created                                                      0.0s
    - Volume "src_sonarqube_logs"        Created                                                      0.0s
    - Volume "src_postgresql"            Created                                                      0.0s
    - Container src-db-1                 Started                                                      5.0s
    - Container src-sonarqube-1          Started                                                      4.2s

In the above output, you can see that the images are downloaded. Also, the volume and network got created after executing the single docker-compose command.

To list the docker-compose containers, we can use the docker-compose ps command,

    docker-compose ps

    NAME                COMMAND                  SERVICE             STATUS              PORTS
    src-db-1            "docker-entrypoint.s…"   db                  running             5432/tcp
    src-sonarqube-1     "/opt/sonarqube/bin/…"   sonarqube           running             0.0.0.0:9000->9000/tcp

The two containers are perfectly running. In this way, we can run multiple containers easily with the help of docker-compose. If you want you can access the soanrqube webapplication in the browser by visiting **localhost:9000**