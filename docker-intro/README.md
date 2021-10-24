# Intro to Docker

When you run an application, you need more than just the code. You need programs installed to compile, interpet, and run the code, and you may need databases or other tools installed and configured. This is difficult enough for one person to do on their personal computer. When you consider the amount of time involved doing this for every developer's computer as well as every server that runs the code, there's a lot of waste. When you consider the impossibility of keeping all of the versions of these tools and configuration data synchronized, there's also a huge surface area for difficult-to-reproduce bugs.

Containers solve both problems by bundling all the programs and configuration needed to run code along with the code itself. This means a computer with Docker installed can run software written in any language and requiring any number of supporting tools and services as easily as they can clone a repository. Moreover, these environments will be identical on everyone's computer, as well as on all the servers.

Docker containers are all virtual Linux environments that have all of their setup and configuration steps saved in files called Dockerfiles. They can be further customized and networked together using a tool called Docker Compose.

## Vocabulary

* **Docker Engine**: A tool for building and running Docker containers
* **Docker Compose**: A tool for coordinating Docker containers, their data, their environments, and the communication between them
* **Docker Hub**: A commercial site operated by Docker Inc. that stores and distributes Dockerfiles
* **Dockerfile**: A text file containing instructions for how to build a Docker image
* **Image**: A compiled Dockerfile
* **Container**: A single instance of a Docker image
* **Host**: The computer running a Docker container

A Docker container is an instance of a Docker image, which is a compiled Dockerfile. Docker containers can be run directly with Docker Engine or with the Docker Compose tool.

## Watch Out!

* If you've used virtual machines before, this may seem similar. Docker serves a similar purpose but is much lighter. A virtual machine is allocated cores of its host's CPU, some of its RAM, and is treated as a totally independent machine. A Docker container is more like a fake operating system that shares the hardware resources of its host computer.
* The Docker platform is free and open-source. There is also commercial company called Docker, Inc. that manages development of the Docker platform, runs Docker Hub, a public repository of Dockerfiles, and provides the Docker Desktop GUI container management software.
* Docker Compose has a significant overlap in functionality with Docker Engine's CLI interface, and many tasks can be executed in either place. In general, the Docker Compose interface is more intuitive, although most non-trivial uses of Docker will require fluency with both.

## Additional Resources

| Resource | Description |
| --- | --- |
| [Docker Docs](https://docs.docker.com/) | Official Docker docs site |
