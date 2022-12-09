---
sidebar_position: 1
---

# Docker

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

Storage location created and managed by Docker located somewhere on your machine with a pregenerated folder name. This is a temporary storage (tmpfs) mechanism for data with a lifecycle linked to your container and is automatically deleted when the container is deleted. Useful for when tuning your container configuration or for files which are not required after the container is removed such as logfiles related to the container itself.

```sh
docker run -name container-name -v /app/anon-volume image
```

#### Names Volumes

Storge managed by Docker but who's life cycle is not linked to containers and which can exist independently. Useful for persistent storage which can be shared between containers such as databases.

```sh
docker run -name container-name -v vol-name:/app/container-volume image
```

<!-- - `src/pages/index.js` → `localhost:3000/`
- `src/pages/foo.md` → `localhost:3000/foo`
- `src/pages/foo/bar.js` → `localhost:3000/foo/bar` -->

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

A new page is now available at [http://localhost:3000/my-react-page](http://localhost:3000/my-react-page).

## Create your first Markdown Page

Create a file at `src/pages/my-markdown-page.md`:

```mdx title="src/pages/my-markdown-page.md"
# My Markdown page

This is a Markdown page
```

A new page is now available at [http://localhost:3000/my-markdown-page](http://localhost:3000/my-markdown-page).
