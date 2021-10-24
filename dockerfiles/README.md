# Dockerfiles

Most of the time, you'll want to create customized Docker images for your apps. This helps them start faster and caches a lot of setup work in the image instead of having to rerun steps like package installation each time.

## Overview of Dockerfiles

A Dockerfile is a text file called `Dockerfile` (no extension), usually placed in the root directory of the app it will contain. Each line of a Dockerfile is an instruction for how the container should be built. These are the most common commands:

| Command | Purpose |
| --- | --- |
| `FROM` | Required, defines what Docker image this container should be based off of. The most minimal image is Alpine Linux with nothing installed, but you'll usually be able to find an image on [Docker Hub](https://hub.docker.com/) that's already configured with the langauages and platforms you want to use. |
| `WORKDIR` | Sets the current working directory that subsequent commands should be relative to. |
| `COPY` | Copy files from your host computer to the container image. |
| `RUN` | Execute a shell command inside of the container. Frequently used to install dependencies, build the app, etc. | 
| `ENV` | Define an environment variable inside the container. |
| `EXPOSE` | Expose a port inside the container to be bound to a port on the host computer. |
| `ENTRYPOINT` | Define a command that should be run whenever a container is started with this image. This is generally not supposed to be overridden. |
| `CMD` | Define a command that should be run on top of the ENTRYPOINT whenever a container is started with this image. This is generally supposed to be overriden when the container is run. |

## Sample Dockerfile

```docker
FROM python:2.7-slim
WORKDIR /app
COPY . /app
RUN pip install -r requirements.txt
EXPOSE 80
ENV NAME World
CMD ["python", "app.py"]
```

This Dockerfile:

1. Uses an official Python image as a base
2. Sets the working directory to `/app`
3. Adds the contents of the folder the `Dockerfile` is in to the `/app` folder inside the container
4. Runs the command `pip install -r requirements.txt` in the `/app` directory
5. Exposes port 80. Note that this doesn't actually make the port available to the host, only makes it eligible to be published when the container is run.
6. Declares an environment variable called `NAME` and sets it to `World`
7. Sets the default command for the container to `python app.py`

## Watch Out!

* The primary difference between `ENTRYPOINT` and `CMD` is their overrideability. If there's a command that the container should always start with, it's an `ENTRYPOINT` that can take a `CMD` as parameters. If there's no command the container should always start with, use `CMD` in the Dockerfile as a default that can be easily overridden whenever starting the container.
* Values in Docker Compose for properties like `expose`, `command`, and `user` will add to the corresponding values in the Dockerfile if they're lists or clobber them if they conflict. Even if you're setting a value in the Docker Compose file, you should still set it in the Dockerfile so the image will still work outside of Docker Compose.

## Additional Resources

| Resource | Description |
| --- | --- |
| [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/) | Official reference for Dockerfiles. |
