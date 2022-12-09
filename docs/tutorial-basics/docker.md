---
sidebar_position: 1
---

# Docker

Just enough virtual for your machine

Docker is like a classic virtual machine but without all the additional fluff which comes along for the ride which takes up unnecessary harddrive space or exposes attack surfaces which don't have to be there in the first place. You don't have to worry about a graphics driver exploit in your database container because it's not needed to function and therefore won't be there.

Single compiled or downloaded images can be shared between multiple containers optimising storage requirements with differences being cached between builds which makes incremental compilations fast.

```sh title='useful-commands'
# Start docker daemon
docker -d

# Open a shell inside a running container:
docker exec -it <container_name> sh

```

## Concepts

### Images

Like the standard image concept from virtual machines and can be downloaded from Docker Hub or complied as custom with extended user configuration. This means that you and your time can build your own image based on an offical Postgres image but with preconfigured settings to suit your development needs.

```sh
# Recommended to always tag images that you build so that if you have multiple test images, you can tell them apart. <image:tag>
# If no tag name is specified then "latest" is used as default tag name
docker build -t tag-name image
```

### Containers

Images, which are read only, are run within containers which can be stopped and started and which allow for read-write modification with additional data such as dependencies and config files required to run your application. Additional data is stored in volumes.

```sh
# Run container and map ports
docker run -p <host_port>:<container_port> <image_name>
```

### Volumes

#### Anonymous Volumes

Storage location created and managed by Docker located somewhere on your machine with a pregenerated folder name. This is a temporary storage mechanism for data storage but will only be removed if a container is run with --rm flag, otherwise it hangs around and a new anonymous volume will be created when the container is run again. I guess this is so that you have the opportunity to inspect these volumes between container development iterations. Useful for when tuning your container configuration or for files which are not required after the container is removed such as logfiles related to the container itself or for dependencies specific to the container only such as npm packages.

```sh
docker run -name container-name -v /app/anon-volume image
```

#### Names Volumes

Storage managed by Docker but who's life cycle is not linked to containers and which can exist independently. Useful for persistent storage which can be shared between containers such as databases.

```sh
docker run -name container-name -v vol-name:/app/container/volume image
```

:::tip External Volumes
Volumes can be located on remote locations such as S3 etc.
:::

#### Bind Mounts

Whenever source files, which are initially copied into an image at build time, are changed, the image must be rebuilt in order for those changes to see them. Bind mounts, as the name suggests, map an _absolute_ folder location into a container such that changes to the local fs are immediately reflected in the container. Useful for project sourcefiles which exist on local fs but requires dependencies etc. in order to function but which reside in a container only. This allows one to work on multiple projects which share the same container such as Python setup for data datascience like Pandas and Numpy.

```sh
docker run -name container-name -v /absolute/path/to/bound/folder:/app/container/bound/volume image
```

:::info
Notice the length of the various mounting options, the longest path (on the container side) is the bind mount with the shortest being anonymous volumes. With docker, the most specific path name takes precedence. This means that even if an application folder is a bindmount, if a named volume is specified in a subfolder of that volume, the named volume shall not be overwritten and the same goes for bindmounts; which is what one would want in practice.
:::

So for example, the following will create an anonymous volume with a named volume nested inside of it (which won't be overwritten but the anon volume) with a bindmount nested further down (which won't be overwritten by either of the previous volumes).

```sh
docker run -d -p 3000:3000 --name docker-app -v named:/app/docker/container -v /absolute/path/to/local/dir:/app/docker/container/path -v /app/docker
```

If you want to be absolutely certain that docker will not overwrite your bindmount, you can tack ":ro" (read only) onto the bindmount location:

```sh
docker run -d -p 3000:3000 --name docker-app -v named:/app/docker/container -v /absolute/path/to/local/dir:/app/docker/container/path:ro -v /app/docker
```

## Anatomy of a Dockerfile

```dockerfile title="Dockerfile"
FROM node

ARG DEFAULT_PORT=80

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE ${DEFAULT_PORT}

CMD [ "node", "app.js" ]

```

```dockerfile title="Dockerfile_from_docker_site"
# syntax=docker/dockerfile:1
FROM ubuntu:22.04

# install app dependencies
RUN apt-get update && apt-get install -y python3 python3-pip
RUN pip install flask==2.1.*

# install app
COPY hello.py /

# final configuration
ENV FLASK_APP=hello
EXPOSE 8000
CMD flask run --host 0.0.0.0 --port 8000
```

:::tip Using latest Docker engine
Using `docker/dockerfile:1`, which always points to the latest release of the version 1 syntax. BuildKit automatically checks for updates of the syntax before building, making sure you are using the most current version.
:::

#### Order of Dockerfile building operations

Behind the scenes, when building an image bases which have already been built previously but with differences within the Dockerfile, docker will use steps already built because each step is cached and only differences are built again which results in efficient builds.
