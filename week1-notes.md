## Virtual Machines
- each VM has its own operating system, resources, and all the internal components needed by any tool or application that will be running on the VM
- benefit is that it gives more control
- drawback is that there is a waste of resources

## Containerization
- there is an additional layer of virutualization on top of the VM
- a much ligher instantiation, lesser duplication
- isolation of the application environment

## Containerization terminology
- Container
    - overall package and virtualized environment that we will be using
    - each application will have its own container
    - configuration of the container is done using a dockerfile
- Image
    - packaged configuration that we need to deploy a running container
    - blueprint for my container
- Registry
    - stores the images
    - its like a source code registry that holds container images when it builds out
    - dockerhub -> stores private or public containers
    - ecr -> amazon elastic container registry


## Docker
- Docker daemon
    - brain of docker that creates and manages docker images 
- Docker CLI
    - gives instructions for the docker daemon to execute
- Image registry
    - place to store our docker images (private/public)
    - Docker images is like a blueprint
- docker run will check if the image exists on the docker host, downloads it from the image registry if it does not exist
- Docker file
    - how my docker images should be built

## Docker process on a high level
- docker file -> docker image -> docker run -> docker container on a docker host

## Docker file semantics
- we need to make sure runtime environment is installed etc
- we need a portable self contained application instance
- set of instructions on how to build a container

```Dockerfile
# The container that has a minimum version of the operating system.
# If we the colon, it will install the latest version.
FROM ubuntu:16.04

# The author of the dockerfile.
LABEL maintainer="rex@yahoo.com"

# The run instruction will define whatever commands we run in a shell.
RUN apt-get update -y && \
    apt-get install -y python-pip python-dev

# This will allow us to copy files from the machine are building the dockerfile on to the image.
COPY ./requirements.txt /app/requirements.txt
# left is the source, right is the destination

# Set the working directory, if the working directory does not exist it will be created.
WORKDIR /app

# Install the dependencies, for this case, all the dependencies are defined in the requirements.txt
RUN pip install -r requirements.txt

# Copy all our source code of application into the /app directory
COPY ./app

# Define the port where our container will listen on
EXPOSE 8080

# What script or command we want to run when our container starts up
ENTRYPOINT ["python"]

# Execute the script that starts up out app
CMD ["app.py"]

# Both entrypoint and command can be overwitten at runtime.
# In our example we are trying to do command: "python app.py" to start the flask application.
# Our entry point is python and our cmd is app.py.


CMD ["executable","param1","param2"] (exec form, this is the preferred form)
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
CMD command param1 param2 (shell form)
```

## Dockerfile semantics
- ADD vs COPY
    - COPY and ADD are somewhat simillar but also different
    - both can be used to copy files and folders from out working computer to the docker image we want to create
    - ADD can be used to download the folder from a remote repo
    - ADD can also be used to extract the files in tar.gz and put it in the destination folder of our image

## Docker images
- each instruction in the dockerfile creates a layer
- layers are files created from each line of commad in the dockerfile and are being stacked together to form the image

## Docker commands to build the app
- docker build -t pythonwebapp .
- each instruction executed is executed in order, creating a layer or use an existing layer
- docker inspect (id for the image)
- we can see all the layers -> each layer is a diff between one layer and another -> stacked together to form an image
- image minimizes duplication -> this is down through a union file system, different container instances may share the same layer
- docker mounts a writable layer on top of the read-only layers
- if we want to change the file on the lower layer, the file will be copied from the read-only layer to the writable layer and modified there
- read only layer will remain unchanged (copy on write strategy)
- smaller image sizes
- **Use Docker Volumes when you need persistent data or to share data across containers**

## Docker CLI
```Bash
docker run hello-world #hello-world image pulled from Docker Hub

#we want to build the image, we give the image a name by using the -t flag, give it a path to find the dockerfile
docker build -t pythonapp

# gives a list of the docker images
docker images

# allows the container to run in detached mode, so the container will be running in the background
# no -d flag means the docker container will hjack our terminal
# -p flag to set which port we are using (host port):(container port)
# next parameter will be the image id
docker run -d -p 8080:8080 702ef3e13c13

docker container ls #this is to list out the containers that we are running

# we want to "log in" to our docker container, just like how we ssh into our vm
# the long chain of code is our container id, we can obtain that using th previous command
docker exec -it bcd8443888f7a bash
# when we are inside the container, we can then run whatever shell commands we like

# "log out of the docker container"
exit

# it shows whatever that is happening in our docker container 
docker logs bcd8443888f7a

# elevate our privilege
sudo su

# we will have a list of all our existing containers
cd /var/lib/docker/containers
ls 

# we can go into the container and view information about our container
cd (container_id)
ls 

# stopping the container
docker stop bcd8443888f7a

# remove the container
docker rm bcd8443888f7a

# we will be given a list of all our docker containers whether it is running or stopped
docker container ps -a
```

## Amazon ECR
- registries are repositories for container images
- dockerhub community contains millions of users and different configurations
- private registries -> we can privately manage our images

## AWS Elastic Beanstalk
- easy to use service for deploying and scaling web applications and services
- we don't need to spend alot of time configuring the infrastructure



## What does AWS Elastic Beanstalk do?
Did this create an EC2 and a VPC for me?

What about security groups, did this open port 80 automatically for us?

Is this load balanced (HA) or not?