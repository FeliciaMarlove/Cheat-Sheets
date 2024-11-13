# Docker cheat sheet

| 💡 When referencing a container, we can just type the first few letters of the ID |
|-----------------------------------------------------------------------------------|

| 📝 | Image = the packaged artifact that can be moved around -> not running |
|----|:----------------------------------------------------------------------|
| 📝 | Container = application running in its environment                    |

* [Images](#images)
* [Containers](#containers)
* [Resources](#resources)

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

`docker exec -it container_name bash`

### Run from a "docker-compose".yml file to run multi-container applications (the file can define multiple services, networks, volumes...)

`docker-compose -f /path/docker-postgres.yaml up -d` 

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
 