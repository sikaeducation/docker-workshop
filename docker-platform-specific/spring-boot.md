# Docker: Spring Boot


## Setup


## Dockerfile

Add the following contents to the Dockerfile:

```docker
FROM openjdk:18-ea-24-bullseye
RUN apt update && apt install -y sudo wget zip unzip && rm -rf /var/lib/apt/lists/*

ARG USER_ID
ENV USER_ID $USER_ID
ARG GROUP_ID
ENV GROUP_ID $GROUP_ID

ARG USER=java
ENV USER $USER

RUN groupadd -g $GROUP_ID $USER && \
    useradd -u $USER_ID -g $USER -m $USER && \
    usermod -p "*" $USER && \
    usermod -aG sudo $USER && \
    echo "$USER ALL=NOPASSWD: ALL" >> /etc/sudoers.d/50-$USER

USER java
WORKDIR /app
RUN curl -s https://get.sdkman.io | bash
RUN chmod a+x "$HOME/.sdkman/bin/sdkman-init.sh"
RUN /bin/bash -c "source $HOME/.sdkman/bin/sdkman-init.sh && sdk install springboot"
COPY --chown=java:java ./app .
RUN ./mvnw install -Dmaven.test.skip=true
CMD ./mvnw spring-boot:run
```

## Compose File

```yaml
services:
  java:
    build:
      context: .
      args:
        USER_ID: $USER_ID
        GROUP_ID: $GROUP_ID
    user: java
    volumes:
      - ./app:/app
    command: ./mvnw spring-boot:run
    ports:
      - "8080:8080"
```

* The `java` service passes the `USER_ID` and `GROUP_ID` environment variables from the host environment into the image. `docker-compose` automatically loads the variables from an `.env` file in the same folder.
* `ruby` is the new user that was created by the container.
* The `rails` service needs to link to the `database` service by including it in `depends_on` to communicate with it.
* The `rails` service needs to bind the IP address `'0.0.0.0'` when starting the server for the site to be accessible outside the container on port 3000
* The `POSTGRES_PASSWORD` must be set for the database image to work, but the other Postgres values can use defaults

## Creating a New Spring Boot App

To scaffold a new Spring Boot app:

1. With the `Dockerfile`, `docker-compose.yml`, and `.env` file filled out, run `docker-compose build` to build the images.
2. Open a shell in the `java` service with `docker-compose run java /bin/bash`
3. Run `spring init`, `unzip demo.zip`, and `rm demo.zip` to scaffold a Spring Boot app

## Running the Server

Just run `docker-compose up`! Any edits you make to local files on your host computer will be immediately reflected in the container.

## Watch Out!

* When other people clone your repo, they'll need to create an `.env` file with their `USER_ID` and `GROUP_ID` as well. The same script will work: `echo "USER_ID=``id -u``\nGROUP_ID=``id -g``" >> .env`. This may be worth adding to a saved shell script.
* `sudo docker-compose build` must be used because the Postgres image always claims root ownership of the volume you mount in it.
* To add gems, make sure that the Unix role `other` has write permissions to the `Gemfile` and `Gemfile.lock` files
* Remember to use `bundle exec rails` commands rather than the system `rails`.

## Sample Spring Boot API

```java
// src/main/java/hello/Application.java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class Application {

  @RequestMapping("/")
  public String home() {
    return "Hello Docker World";
  }

  public static void main(String[] args) {
    SpringApplication.run(Application.class, args);
  }

}
```

## Additional Resources

| Resource | Description |
| --- | --- |
| [Docker Hub: Open JDK](https://hub.docker.com/_/openjdk) | Official Java Docker images |
| [Spring Boot with Docker](https://spring.io/guides/gs/spring-boot-docker/) | Spring's guide to Spring Boot on Docker |
