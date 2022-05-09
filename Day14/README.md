# Day 14: Ignore the files using .dockerignore form the docker build context

## Docker build context

When you are building a docker image from the docker command line(docker CLI) using Dockerfile, the files/folders from the docker CLI has been sent to the docker server or daemon, after the docker build happens and the files/folders will be included in the docker container. Those files/folders are called **docker build context.**

If the docker build context is larger, your docker image size increases, as well as the build time, increases.

## .dockerignore file

Not all the files and folders from the docker build context are needed to be included in the docker image. Items like secret files and the .git folder should be excluded from the docker container. To do so, we are going to use the **.dockerignore** file(similar to the .gitignore file) where we have to list the files/folders we want to exclude from the docker container. 

## How does the dockerignore works?

According to docker's official documentation

    Before the Docker CLI sends the context to the Docker daemon, it looks for a file named .dockerignore in the root directory of the context. If this file exists, the CLI modifies the context to exclude files and directories that match patterns in it. This helps to avoid unnecessarily sending large or sensitive files and directories to the daemon

Here is an example .dockerignore file:

    # comment
    */temp*
    */*/temp*
    temp?

The above .dockerignore file causes the following build behaviour:

|Rule|Behavior|
|:-----|:--------|
|#comment|Ignored|
|\*/temp*|Exclude files and directories whose names start with temp in any immediate subdirectory of the root. For example, the plain file /somedir/temporary.txt is excluded, as is the directory /somedir/temp.|
|\*/\*/temp*| Exclude files and directories starting with temp from any subdirectory that is two levels below the root. For example, /somedir/subdir/temporary.txt is excluded.|
|temp?|Exclude files and directories in the root directory whose names are a one-character extension of temp. For example, /tempa and /tempb are excluded|

## Hands-on

Let's create a .dockerignorefile and exclude the unnecessary files from the build context while building the docker image.

    # .dockerignore
    unwanted-file.txt
    *.yml

From the above .dockerignore file, we can understand that the file named unwanted-file.txt and the file ends with .yml will be excluded from the docker build context.  

Below, I've listed the current build context from the docker CLI.  

Once we build the docker image, the file named unwanted-file.txt and unwanted-file.yml will be excluded. Only the file named required-file.txt and Dockerfile will be present.

    05/09/2022  06:22 PM    <DIR>          .
    05/09/2022  06:22 PM    <DIR>          ..
    05/09/2022  06:23 PM               124 .dockerignore
    05/09/2022  06:21 PM               113 Dockerfile
    05/09/2022  06:22 PM                67 required-file.txt
    05/09/2022  06:20 PM                67 unwanted-file.txt
    05/09/2022  06:20 PM                67 unwanted-file.yml

Next, we will create a Dockerfile and build the docker image,

    # Dockerfile
    FROM debian:10.12-slim
    LABEL maintainer="navinprasadk"
    COPY . /app 
    CMD ["/bin/sh", "-c", "tail -f /dev/null"]

In the Dockerfile, using COPY instruction we are adding the files from the Docker build context in the Docker CLI to the **/app** directory in the container

    docker build -t debian_dockerignore:v1 .

Image has been built. Now, let's run the container,

    docker run --detach --rm --name dockerignore_con debian_dockerignore:v1
    62b79b27c1550f641b1a998ceb182e27f886bb38f2aec87844e8b632ba14e624

    docker ps
    CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS         PORTS     NAMES
    62b79b27c155   debian_dockerignore:v1   "/bin/sh -c 'tail -f…"   3 seconds ago   Up 2 seconds             dockerignore_con

The container has been created and let's check what are all the files/folders present in the /app directory inside the container,

    docker exec dockerignore_con ls /app
    Dockerfile  required-file.txt

Only the Dockerfile and required-file.txt are present. The file named unwanted-file.txt and unwanted-file.yml are excluded from the build context.
