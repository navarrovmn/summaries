# Why?
___ 
* More lightweight than a VM (doesn't replicate the whole OS and runs on top of the machine Kernel);
* Layers are used as "cache" for other images to use, so as to be more efficient in size;
* Only `RUN`, `COPY` and `ADD` create layers;

# Commands
___ 

```
$ docker run hello-world
$ docker pull python:3.5
$ docker run python:3.5
$ docker ps -a
$ docker run -it python:3.5 bash
$ docker exec -it <container_id> bash
$ docker start <container_id>
$ docker commit -m "<commit message>" <container_id/name> <new_image_name>:<version>
$ docker volume create project_directory
$ docker run -it --name "first_container" -v /usercode/app_files:/app_files python:3.8 bash
```

To push a new version of an created image:
* `docker login` 
* `docker tag <image_name> <username>/<image>:<version>
* `docker push <username>/<image>:<version>

Example:

```
$ docker tag date_project:1.0 username/date_project:1.0
$ docker push username/date_project:1.0
```

Spreadsheet:

![[docker-commands.png]]

# Volumes
___ 
* Way to share data between containers (more than share host/container);

# Dockerfile
___
Commands:

```
FROM
WORKDIR
ENV
COPY
RUN

CMD - Runs command inside the container. Only one per Dockerfile. If more than one, runs the last one

ENTRYPOINT - Configures the container that runs as an executable, meaning that you can create containers that mimic the behavior of standalone command-line tools. If more than one, runs the last one.
```

Helpful commands:

``` BASH
$ lsof -i TCP:50000 # checks the processes using a TCP port 50000
```

# Network
___

* By default, all containers run in the same network space of Docker. So, every container can communicate with others by using user defined network (link option is deprecated);

# Docker-Compose
___
* Manages multiple containers to compose a service (think of a web-application, DB, cache);

Example:
``` YAML
version: '3'
services:
  web:
    # Path to dockerfile.
    # '.' represents the current directory in which
    # docker-compose.yml is present.
    build: .

    # Mapping of container port to host
    
    ports:
      - "5000:5000"
    # Mount volume to not create an image for every change on source code
    volumes:
      - "/usercode/:/code"

  database:

    # image to fetch from docker hub
    image: mysql/mysql-server:5.7

    # Environment variables for startup script
    # container will use these variables
    # to start the container with these define variables. 
    environment:
      - "MYSQL_ROOT_PASSWORD=root"
      - "MYSQL_USER=testuser"
      - "MYSQL_PASSWORD=admin123"
      - "MYSQL_DATABASE=backend"
    # Mount init.sql file to automatically run 
    # and create tables for us.
    # everything in docker-entrypoint-initdb.d folder
    # is executed as soon as container is up nd running.
    volumes:
      - "/usercode/db/init.sql:/docker-entrypoint-initdb.d/init.sql"
    
```

Commands:
```BASH
$ docker-compose build
$ docker-compose images
$ docker-compose run web
$ docker-compose up
$ docker-compose stop
$ docker-compose rm
$ docker-compose start
$ docker-compose restart
$ docker-compose restart
$ docker compose ps
$ docker-compose [prune|down]
$ docker-compose logs
```