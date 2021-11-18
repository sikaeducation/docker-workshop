* What is the only required top-level key in a Docker Compose file?
    `services`
* What is the purpose of Docker Compose?
    Defines the containers and how they work
* What's the difference between a shell-form command and an exec-form command?
    In shell-form a shell is invoked to run the command, whereas in exec-form it is not
* What is the alternative to volumes in Docker? What are the tradeoffs between them?
    An alterntative to volumes are bind mounts.
    When you use a bind mount, a file or directory on the host machine is mounted into a container. The 
    file or directory is referenced by its absolute path on the host machine. By contrast, when you use a 
    volume, a new directory is created within Docker’s storage directory on the host machine, and Docker 
    manages that directory’s contents.
    
    Bind mounts are very performant, but they rely on the host machine’s filesystem having a specific 
    directory structure available. Volumes are the preferred mechanism for persisting data generated and used by containers. Some advantages of volumes over bind mounts are:
    - easier to backup or migrate
    - they do not increase the size of the container, because they exist outside of it
    - can be managed with the Docker CLI or Docker API
    - work on both Linux and Windows containers
    - can be more safely shared between containers
    - new volumes can be prepopulated by a container
    - volumes on docker desktop have much higher performance than bind mounts from Mac or Windows hosts
* How can you find out which tag to use for a Docker image?
    After the colon next to the image name, e.g. `node:latest`
* When mounting volumes in Docker, which order is the mapping declared in?
    First is the path to the folder in the host, followed by the path inside the container
* When mounting volumes in Docker, do the folders use relative or absolute paths?
    Absolute
* What does it mean to map a port in Docker?
    When a port in the host is connected to a port in a docker container
* How do you run multiple commands in a Docker `command`?
    With the bracket notation `[]`
* How do you start a Docker Compose app?
    `docker-compose up`
* What's the difference between `docker-compose run` and `docker-compose exec`?
    `docker-compose run` - run a one-off command
    `docker-compose exec` - run a command in a running container
* In Docker Compose, how do you start a terminal in a running service named `web`?
    `docker-compose exec -it web /bin/bash`
* Why should Docker port mappings be wrapped in quotes in Docker Compose files?
    So YAML doesn't incorrectly parse it as a sexagecimal (?) number
* In Docker Compose, if you want to start a service with `rails s -b 0.0.0.0`, how would write the `command`?
    ```yaml
    command: sh -c "rails s -b 0.0.0.0"
    ```
* What does the `--build` flag do on the `docker-compose up` command?
    Builds the container if it hasn't already been build
* What is the `:latest` tag on Docker images and what considerations need to be made when using it?
    Points to the latest version of the image. It's not recommended to use `:latest` because it is always changing with new releases
    and this would defeat the purpose of ensuring all servers running the app are running the same dependencies and versions.
