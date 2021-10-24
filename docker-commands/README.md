# Docker Commands

These commands operate directly on Dockerfiles, Docker images, and Docker containers. Note that many of these commands have equivalents in `docker-compose` that are easier to use, but you may find yourself needing to work with individual containers outside of the app occasionally.

| Command | Purpose |
| --- | --- |
| `docker build -t <image name> <path to Dockerfile>` | Create an image from a Dockerfile. |
| `docker run <image name> [optional commands]` | Start a container from an image, optionally overriding the `CMD` or parameters to the `ENTRYPOINT`. |
| `docker stop <container id>` | Stop a container. |
| `docker restart <container id>` | Restart a container. |
| `docker exec <container id> <command>` | Run a command in a running container |
| `docker exec -it <container id> /bin/bash` | Open an interactive terminal in a running container. |
| `docker ps` | See a list of Docker containers that are currently running on the system. Often used to get IDs of containers. |
| `docker image ls` | See a list of Docker images you've already pulled. Often used to see how much space your images are taking up. |
| `docker system prune -a` | Delete all of your images to recover hard drive space. To use them, rebuild them. |

## Watch Out!

* When referencing Docker containers, you don't need to type the entire ID, only the first 4 characters. You can also name a container by passing the `--name <name>` flag when starting a container and using the name instead of the ID.
* All of these commands also work with containers created with Docker Compose.
* Note that on Linux, Docker commands need to be run as a superuser with `sudo` unless you've completed the [Docker post-installation instructions for Linux](https://docs.docker.com/engine/install/linux-postinstall/)

## Additional Resources

| Resource | Description |
| --- | --- |
| [Docker Engine CLI Reference](https://docs.docker.com/engine/reference/commandline/cli/) | Official reference for Docker commands. |
