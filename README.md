# Docker
**This document is the complete version with each Docker feature explained.**  
**See the list of features below if you want to see just one that interests you.**

## Docker features
🟢 [Docker Images](https://github.com/nadmax/dock-help/blob/master/images/README.md)  
🟢 [Docker Containers](https://github.com/nadmax/dock-help/blob/master/containers/README.md)  
🟢 [Docker Volumes](https://github.com/nadmax/dock-help/blob/master/storages/volumes/README.md)  
🟢 [Docker bind mounts](https://github.com/nadmax/dock-help/blob/master/storages/bind/README.md)  
🟢 [Dockerfile](https://github.com/nadmax/dock-help/blob/master/dockerfile/README.md)  
🟡 [Docker Networks](https://github.com/nadmax/dock-help/blob/master/networks/README.md)    
🟡 [Docker Compose](https://github.com/nadmax/dock-help/blob/master/compose/README.md)  
🔴 Docker Context  
🔴 Docker System  
🔴 Docker Swarm

*(🟢 means completed, 🟡 means in progress, 🔴 means not covered)*

## What is Docker?
Docker is an open-source platform which provides the ability to package and run an application in an isolated environment called container.   
Thanks to the isolation, it allows you to run many containers simultaneously on a given host.  

You can share containers while you work, it ensures that everyone gets the same container that works in the same way.

## Installation
They are two ways to install Docker:  
- [Docker Desktop](https://www.docker.com/products/docker-desktop/): A user-friendly, all-in-one application for container management.
- [Docker Engine](https://docs.docker.com/engine/install/): The containerization technology for building and containerizing your applications.

## General commands
```bash
docker -d # Start the docker daemon
docker <optional_subcommand> --help # Get help with Docker. Works with subcommand like build, images etc...
docker info # Display system-wide information
```

## Images
Before running a container, you need an image of your application.  

A Docker image is a lightweight, standalone, executable package of software that includes everything needed to run an application (code, runtime, system tools, system libraries and settings).  
Once an image is created, it can't be modified.  
You can only make a new image or add changes on top of it.  
Images on a container are composed of layers, representing a set of file system changes that add, remove, or modify files.  

Here are some examples of Docker commands related to images:
```bash
docker build -t <image_name> . # Build an image from a Dockerfile
docker build -t <image_name> . --no-cache # Build an image from a Dockerfile without the cache
docker images # List local images
docker rmi <image_name> # Delete an image
docker image prune # Remove all unused images
docker rmi -f $(docker images -aq) # Delete all images
docker inspect <image_name> # Show low-level image info (in JSON format)
docker history <image_name> # Show the image history by listing its ancestors
docker tag <repo_name>/<image_name>|<image_name>:<tag_name> # Tag an image
docker push <repo_name>/<image_name>|<image_name>:<tag_name> # Push an image/repo to a registry
docker pull <repo_name>/<image_name>|<image_name>:<tag_name> # Pull an image/repo from a registry
docker search <text> # Search an image on the official registry
```

## Containers
A container is a runtime instance of a Docker image.  
A container will always run the same, regardless of the infrastructure.  

They isolate software from its environment and ensure that it works uniformly despite differences for instance between development and staging.

Here are some examples of Docker commands related to containers:
```bash
docker run --name <container_name> <image_name> # Create and run a container from an image, with a custom name
docker run -p <host_port>:<container_port> --name <container_name> <image_name> # Create and run a container with a custom name and publish a container's port to the host.
docker run -d --name <container_name> <image_name> # Create and run a container with a custom name, in the background
docker start|stop <container_name>|<container_id> # Start or stop an existing container by specifying its name or its ID
docker rm <container_name> # Remove a stopped container
docker exec -it <container_name> # Open a shell inside a running container
docker logs -f <container_name> # Fetch and follow container logs
docker container ls # List containers
docker container inspect <container_name>|<container_id> # Inspect a running container (in JSON format)
docker ps # List currently running containers
docker ps -a|--all # List running and stopped containers
docker ps -q # Only list container IDs
docker container stats # View resource usage stats
docker commit <container_name> <image_name> # Commit a snapshot of the container
```

## Volumes
Volumes are persistent data stores for containers.  
When you create a volume (see examples below), it's stored within a directory on the Docker host.  
When you mount the volume into a container (see examples below), this directory is what's mounted into the container.  
By default, mounted volumes are read-write volumes but you can mount a volume as read-only.

Volumes are managed by Docker and are isolated from the core functionality of the host machine.
If you need to access files or directories from both containers and the host, use bind mounts.

Here are some examples of Docker commands related to volumes:
```bash
docker volume create <volume_name> # Create a new volume
docker volume ls # List volumes
docker volume inspect <volume_name> # Show low-level volume info (in JSON format)
docker volume rm <volume_name> # Delete a volume
docker run -d --name <container_name> --mount source=<volume_name>,target=<directory> <image_name> # Start a detached container with a volume
docker run -d --name <container_name> -v <source_volume>:<target_directory> <image_name> # Shortest way to start a detached container with a volume
docker run -d --name <container_name> --mount source=<volume_name>,target=<directory>, readonly <image_name> # Start a detached detached container with a read-only volume
docker run -d --name <container_name> -v <source_volume>:<target_directory>:ro <image_name> # Shortest way to start a detached container with a read-only volume
```

## Bind mounts
Bind mounts create a direct link between a host system path and a container, allowing access to files or directories stored anywhere on the host.
By contrast, they aren't isolated by Docker.  
Which means, if you specified a mount path that doesn't exist on the host, it will produce an error.  
Both non-Docker processes on the host and container processes can modify the mounted files simultaneously.  

Use bind mounts when you need to be able to access files from both the container and the host.

Here are two examples of Docker commands related to bind mounts:
```bash
docker run -d --name <container_name> --mount type=bind,src=<host-path>,dst=<container-path> <image_name> # Start a detached container with a bind mount
docker run -d --name <container_name> --volume <host-path>:<container-path> <image_name> # Shortest way to start a detached container with a bind mount
```

## Networks
Networking for containers refers to the ability to connect and communicate with each other, or to non-Docker workloads.  
Networking is enabled by default, which means they can make outgoing connections.  
A container only sees a network interface with an IP address, a gateway, a routing table, DNS services, etc...  

You can create custom networks, and connect multiple containers to the same network.  
Once connected to a custom network, containers can communicate with each other using container IP addresses or container names.

Here are some examples of Docker commands related to networks:
```bash
docker network create -d <driver_type> <network_name> # Create a custom network by specifying driver
docker network ls # List networks
docker network inspect <network_name> # Show low-level network info (in JSON format)
docker network connect <network_name> <container_name> # Connect a container to a network
docker network disconnect <network_name> <container_name> # Disconnect a container from a network
docker network rm <network_name> # Delete network
docker network prune # Delete unused networks
docker run -d -v <source_volume>:<target_directory> <image_name>  --name <container_name> --network <driver_type> <image_name> # Start a container with a volume mount from a network driver
```

### Drivers
Drivers are pluggable interfaces implementing networking functionality for containers.  
Docker provides built-in network drivers for common use cases, including multi-host networking and encryption.  

Here are a list of some drivers:
| Driver | Description |
|----------------|-------------|
| **Bridge**     | A software device which lets containers connected to the same network, while providing isolation from containers that aren't connected to that network. It is the default network driver if you don't specify one. |
| **Host**       | Remove network isolation between the container and the Docker host, and use the host's networking directly. 
| **overlay** | Connect multiple Docker daemons together and enable Swarm services and containers to communicate across nodes.
| **none** | Completely isolate a container from the host and other containers. Not available for Swarm services.

## Dockerfile
A Dockerfile is a text document containing all the instructions a user could call on the command line to create a Docker image.  
Instructions are not case-sensitive. However, convention is for them to be uppercase to distinguish them from arguments more easily.

Here's the instructions you can use:
| Instruction   | Description                                               |
|---------------|-----------------------------------------------------------|
| ADD           | Add local or remote files and directories.                |
| ARG           | Use build-time variables.                                 |
| CMD           | Specify default commands.                                 |
| COPY          | Copy files and directories.                               |
| ENTRYPOINT    | Specify default executable.                               |
| ENV           | Set environment variables.                                |
| EXPOSE        | Describe which ports your application is listening on.    |
| FROM          | Create a new build stage from a base image.               |
| HEALTHCHECK   | Check a container's health on startup.                    |
| LABEL         | Add metadata to an image.                                 |
| ONBUILD       | Specify instructions for when the image is used in a build. |
| RUN           | Execute build commands.                                   |
| SHELL         | Set the default shell of an image.                        |
| STOPSIGNAL    | Specify the system call signal for exiting a container.   |
| USER          | Set user and group ID.                                    |
| VOLUME        | Create volume mounts.                                     |
| WORKDIR       | Change working directory.                                 |

[View the Dockerfile example here](https://github.com/nadmax/dock-help/blob/master/dockerfile/Dockerfile.example)

## Docker Compose
Docker Compose is a tool for defining and running multi-container applications.  
Compose simplifies the control of your entire application stack, making it easy to manage services, networks, and volumes in a single YAML configuration file.  
With a single command, you can create and start all the services from your configuration file. (see examples of compose commands below)

```bash
docker compose up -d # Start all detached services defined in compose.yaml file
docker compose down # Stop and remove running services, networks
docker compose logs # Monitor running containers output
docker compose ps # List all the services with their current status
```

[View the compose file example here](https://github.com/nadmax/dock-help/blob/master/compose/compose.example.yaml)
