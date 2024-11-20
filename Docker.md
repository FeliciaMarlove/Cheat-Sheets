# Docker cheat sheet

| 💡 When referencing a container, we can just type the first few letters of the ID |
|-----------------------------------------------------------------------------------|

| 📝 | Image = the packaged artifact that can be moved around -> not running |
|----|:----------------------------------------------------------------------|
| 📝 | Container = application running in its environment                    |

* [Images](#images)
* [Containers](#containers)
* [Resources](#resources)
* [Docker-Compose](#docker-compose)

## Images

### Pull an image (ex latest nginx alpine)

`docker pull nginx:alpine`

### Create an image from a Dockerfile (-t = tag)

`docker build -t image-name .`

### See all images

`docker images`

### Delete an image

`docker rmi image_name`

### Delete all images (forcing)

`docker rmi $(docker images -a -q) --force`

## Containers

### Run an image in a container 

Exposes port 80 in the container on port 8085 on host
,`-d` to detach: prevent the terminal to switch to the container logs => it will only give the container id and stay in current terminal and run the container in background
, container name is optional (otherwise it makes up a name)

`docker run -d --name container_name -p 8085:80 image_name`

### Run and show files in the container

`docker run image_name ls /working_dir`

### Run and create a persistent volume on host 

Anything that will be written to the mentioned folder will be persisted in the same folder hierarchy on the host; hierarchy will be created if needed

`docker run -v /path/to/folder image_name`

### Run and create a persistent volume on host with a custom location

What's written to `/path/to/folder` in the container will be persisted in `$(pwd)` on host (pwd = Print Working Directory on Mac/Linux).
`$(pwd)` is the place from where the container is run.
Windows PowerShell version of the variable is `${PWD}`

`docker run -v $(pwd):/path/to/folder image_name`

### Run, remove container when it exits, use interactive terminal session, mount the current directory (pwd) on the host to /src (allows the container to access files from your current directory), use Python3 image, execute local hello.py located in /src on the container

`docker run -rm -it -v $(pwd):/src python:3 python /src/hello.py`

### Run and open bash

`docker run -rm -it -v $(pwd):/src bash`

### Open the bash console of a running container

You can/should use `sh` (or `PowerShell` for Windows systems containers) instead of `bash` depending on what the container supports

`docker exec -it <container_name or container_id> bash`

### Get container logs

`docker logs container_name`

### See running containers

`docker ps`

### See all containers (even inactive)

`docker ps -a`

### Stop a container

`docker stop <container_name OR container_name>`

### Delete a stopped container

`docker rm <container_name OR container_id>`

### Delete a container even if running

`docker rm -f container_name`

## Resources

### Remove unused resources (stopped containers, unused networks, dangling images that are not tagged or referenced by any container, unused build cache)

By default, it does not include volumes, to do so add the flag `--volumes`

`docker system prune`

### Remove all unused resources (same + also remove unused images that are not currently being used by any container)

`docker system prune -a`

### List networks

There are (3) default networks created by Docker

`docker network ls`

### Create a network

There are other options for the driver type, bridge is the simplest to implement

`docker network create --driver bridge network_name`

### Run a container in a specific network

`--net` is a shorthand for `--network`

Containers running in a same network can communicate with each other

`docker run -d --net=network_name --name=container_name image_name`

### Inspect a network

See network config and containers inside that network

`docker network inspect <network_id or network_name>`

### Remove a network

`docker network rm network_name`

## Docker compose

Run multi-container applications (the file can define multiple services, networks, volumes...) and avoid retyping the same sequence of CLI commands over and over again.

Advantages:
* All in one file instead of typing several times the same commands for each service
* Whole lifecycle management (build/start/stop/rebuild, view status of running services, log streaming...)
* Configuration as code

When the file name is `docker-compose.yml`, you don't need to specify it in the command. Other, you'll need to add the flag `-f /path/docker-my-app.yaml` before the command.

Use the `-d` flag for detached.

Newer versions of Docker allow to use the command `docker compose` as well as `docker-compose`

### Build the image

Needed for custom images

`docker-compose build`

### Run the container

Default

`docker-compose up`

With specific file and detached

`docker-compose -f /path/docker-postgres.yaml up -d`

### Stop and remove the resources created by the `up` command

`docker-compose down`

### Pass an argument from the command line

Accessed with ${ARG_NAME}

`export ARG_NAME=arg_value`

`docker-compose build`

### Push images to a registry

Will push all images in the docker-compose file, taking into account the environment variables we set up

`docker-compose push`
 
### docker-compose.yml basic structure

```
version: "3.x"
services:
    [list of services: apps, databases...]
    service_xyz:
        container_name: custom_name_for_service_xyz
        image: image_name
        build: (where to get the docker file, context...= how to build => for custom image builds)
            context:
            dockerfile: (when the name is not the default "dockerfile")
            args: (arguments passed at build time, accessible with $MY_ENV_VAR)
                MY_ENV_VAR: some_value
        environment:
            - MY_OTHER_ENV_VAR: some_value (arguments passed at runtime) => there is another way to pass arguments via a file
        networks: (networks the app is part of)
            - ...
        ports:
            - "external:internal"
        volumes:
            - ...
        depends_on:
            - service_abc
    service_abc:
        ... 
networks:
    [list of networks]
    network_def:
        driver: bridge (driver type)
```