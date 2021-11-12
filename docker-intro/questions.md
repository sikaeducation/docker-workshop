* What does Docker do?
    - Docker provides a lightweight and standardized way to package software in containers. It ensures consistency of the application's dependencies and their versions in any machine and across various environments.
* What advantages does Dockerizing an app have?
    - Prevents bugs related to mismatched dependency versions (because such dependencies would be exactly the same in all hosts).
    - It is more lightweight than virtual machines because containers can share the host's resources (CPU and RAM) with each other.
* Differentiate between containerization and virtual machines.
    - Virtual machines act as indepent machines, similar to a physical machine, and have their own OS on top of the host OS.
    - On the other hand, containers don't require their own OS. Instead they can share the host OS, which makes them more lightweight than VMs.
    - Containers can share resource with each other, which makes them faster to create and scale up or down depending on their load.
    - Virtual machines need to have resources allocated more permanently, so are not easy to scale up or down like containers.
* What is the difference between a Docker Image and a Docker Container?
    - A Docker image is the compiled Dockerfile (instructions in machine readable code)
    - A Docker container is an instance of the image running on a host machine
* What is the difference between a Dockerfile and a Docker Image?
    - The Dockerfile is a set of instructions for compiling an image
    - The Image is the output of the Dockerfile; all of the binaries and executables
* What is the difference between Docker and Docker, Inc.?
    - Docker is the open source platform whereas Docker, Inc. is the company that maintains Docker
* What operating system do Docker containers use?
    - Linux
