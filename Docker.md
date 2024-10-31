# Docker cheat sheet

| 📝 | Image = the packaged artifact that can be moved around -> not running |
|----|:----------------------------------------------------------------------|
| 📝 | Container = application running in its environment                    |

### See all containers (even inactive)

`docker ps -a`

### Create an image from a Dockerfile (-t = tag)

`docker build -t image-name .`

### Run and show files in the container

`docker run image_name ls /working_dir`

### Run an image in a container and exposes port 80 in the container on port 8085 on host (-d = detached mode, in background)

`docker run -d --name container_name -p 8085:80 image_name`

### Delete all images (forcing)

`docker rmi $(docker images -a -q) --force`

### Get container logs

`docker logs container_name`

### Delete an image

`docker rmi image_name`

### Delete a container even if running

`docker rm -f container_name`

### Remove unused resources

`docker system prune`

### Remove all unused resources (all stopped containers, networks not used by a container, images without associated container...)

`docker system prune -a`

### Run, remove container when it exits, use interactive terminal session, mount the current directory (pwd) on the host to /src (allows the container to access files from your current directory), use Python3 image, execute local hello.py located in /src on the container

`docker run -rm -it -v $(pwd):/src python:3 python /src/hello.py`

### Open bash

`docker run -rm -it -v $(pwd):/src bash`

### Open the bash console of a running container

`docker exec -it container_name bash`

### Run from a "docker-compose".yml file to run multi-container applications (the file can define multiple services, networks, volumes...)

`docker-compose -f /path/docker-postgres.yaml up -d` 

 