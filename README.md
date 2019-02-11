# Dockerfile Commands (Instructions)


### ADD
The `ADD` command copies the files from the source on the host into the container's own filesystem at the set destination. Source can also be an URL.

```docker
# Usage: ADD [source directory or URL] [destination directory]
ADD /my_app_folder /my_app_folder
```

### CMD

The `CMD` command executes a specific command. Unlike RUN, it is not executed during the build but at the end of the container initiation. Therefore, it should be considered as an initial, default command that gets executed with the creation of the container. `CMD` command also replaces any command passed during the creation of the container.

```docker
# Usage 1: CMD application "argument", "argument", ..
CMD "echo" "Hello docker!"
```

### ENTRYPOINT

`ENTRYPOINT` sets the default *application* that is used every time container is created using the image. If you install a specific application inside an image and want to use this image to only run this application, you can state it with `ENTRYPOINT`. Whenever a container is created from that image, your application will be target. 

> Note that `ENTRYPOINT` specified the application and not the whole command.

```docker
ENTRYPOINT python
```

### ENV

The `ENV` command is used to set the environment variables (one or more).

```docker
ENV SERVER_WORKS 4
```

### EXPOSE

The `EXPOSE` command associates a specified port to enable networking between the running processes inside the container and the host.

```docker
# Usage: EXPOSE PORT-A PORT-B
EXPOSE 8080
```

### FROM

`FROM` defines the base image to use to start the build process. It can be any image, including the ones you created previously. In case an image is not found locally, it will try to pull the image from the *[Docker Hub](https://hub.docker.com)*. It is the first command inside a *Dockerfile*.

```Docker
FROM ubuntu
```
```Docker
FROM python:2.7.10
```

### MAINTAINER

A non-executing command declaring the author. It should come after `FROM`.

```Docker
MAINTAINER author_name
```

### RUN

The `RUN` command takes a command as its argument and runs it to form the image. Unline `CMD`, it is actually used to build the image.

```Docker
RUN apt update && \
    apt upgrade && \
    apt install build-essential
```

### USER

The `USER` directive changes the UID used to run any commands that follow it.

```Docker
FROM ubuntu:16.04

RUN apt-get update && \
    apt-get -y install sudo

RUN mkdir -p /newfolder
RUN adduser newuser
RUN chown newuser /newfolder

USER newuser
# All the commands from here will be run as newuser
WORKDIR /newfolder
```

### VOLUME

The `VOLUME` directive creates a mount point and marks it as holding externally mounted volumes from native host or other containers. The `docker run` command initializes the newly created volume with any data that exists at the specified location within the base image. 

```docker
# Usage: VOLUME ["/dir1", "/dir2" ..]
FROM ubuntu
RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting
VOLUME /myvol
```
So, `docker run` command will create a mount point in the host machine and binds it with `/myvol` from the container. It will also copy all the data from `/myvol` to the mount point.

In order to specify the mount point while running a container:
```bash
docker run --volume=/opt:/myvol image_name
```

### WORKDIR

The `WORKDIR` directive sets the working directory for any `RUN`, `CMD`, `ENTRYPOINT`, `COPY` and `ADD` instructions that follow it in the Dockerfile.

### Building an image from Dockerfile

```bash
# Build an image using the Dockerfile at current location
# Example: docker build -t [name] .
docker build -t repo_name:tag .
```

```bash
# Pull docker images from Docker Hub
docker pull name:tag
docker pull ubuntu:latest
docker pull python:2.7.10
```

```bash
# To run a container from an image
docker run source_image
docker run -it source_image bash
docker run -it --rm --name container_name source_image bash
docker run -v ~/Downloads:/home:rw -p 6969:22 source_image
docker --cpus="2" run -v 

# Get inside a container
docker exec -it container_name bash
docker attach container_name # ctrl+p followed by ctrl+q to detach

# Get logs from the container without attaching
docker logs -f container_name # -f, --follow. ctrl+c to exit

# start a container
docker start container_name

# stop a container
docker stop container_name
```

### Example (running parity using docker)

```bash
docker pull parity/parity:v2.2.9 # to pull image from docker registry

docker run -d 
           --cpus="2" 
           --name="2-cpu" 
           -p 8545:8545 -p 8546:8546 -p 30303:30303 -p 30303:30303/udp 
           -v ~/.local/share/io.parity.ethereum/docker/:/home/parity/.local/share/io.parity.ethereum/ 
           parity/parity:v2.2.9 
           --base-path /home/parity/.local/share/io.parity.ethereum/ 
           --config /home/parity/.local/share/io.parity.ethereum/config.toml 
           --pruning fast 
           --jsonrpc-cors "null" 
           --jsonrpc-interface all 
           --jsonrpc-hosts all
```
