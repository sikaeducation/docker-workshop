# Docker: Multi-Stage Builds

It's common for projects to end up with multiple Dockerfiles. For example, a team might use one Dockerfile for development, another one for testing, and yet another one for production. Alternatively, teams might create a Dockerfile for scaffolding and another for running the code. It's cumbersome to keep the shared parts of these files synchronized. The solution is multi-stage builds.

## Defining Multi-Stage Builds

A multi-stage build is a Dockerfile with more than one `FROM` statement. There are 3 common patterns for these: Using files from earlier stages, using previous images, and using files from external images.

### Using files from earlier stages

A common use for multi-stage builds is to build an app in one stage and then optimize it in a second:

```dockerfile
FROM node:16-bullseye as dev
COPY ./app /app
WORKDIR /app
RUN npm install && npm run build
CMD npm start

FROM node:16.13.0-alpine3.13 as prod
COPY --from=dev /app/dist /app
WORKDIR /app
CMD node index.js
```

This Dockerfile has two stages to it. The first copies app code into it, installs all the dependencies, and builds the app. The second stage copies the built app into a smaller, slimmer image that doesn't contain the dependencies. This allows the final image to be much smaller and efficient when deployed.

Some differences from a regular Dockerfile:

* Multiple `FROM` statements in one file
* Each `FROM` statement is given a name
* The base images are different; the first uses a large Ubuntu image, while the second uses a tiny optimized Alpine image
* The `COPY` command following the second `FROM` has a `--from=dev` flag that indicates that the source of the `COPY` should be from the image at the `dev` stage, not the host filesystem

### Building on top of previous stages

You can also use one stage as the base for another. For example, a Rails app might have one stage used to scaffold the app, and then another stage to run the app:

```dockerfile
FROM ruby:3.0.3 as base

# ...Install dependencies...
# ...Setup non-root user...

WORKDIR /app
USER $USER
RUN gem install bundler

# development stage
FROM base as dev
COPY ./app/Gemfile* ./
RUN bundle install
CMD [rails, s, -b, "0.0.0.0"]
```

If you tried to build the entire thing as one stage, it would fail. This is because the `Gemfile` doesn't exist and there's nothing to `bundle install` yet.

### Using files from external images

Lastly, you can reference external images (including images from Docker Hub) directly in `COPY` statements as well:

```dockerfile
COPY --from=nginx:latest /etc/nginx/nginx.conf /nginx.conf
```

This helpful if a particular image has source or configuration files you want to include without also bringing the rest of the image in.

## Using Multi-Stage Builds

### Docker Compose

In Docker Compose, you can run a service based on a particular stage by setting the `target` of the `build`:

```yaml
services:
  backend:
    build:
      context: ./backend
      target: dev
```

Any `docker-compose build`, `docker-compose run`, etc. commands will use the image at the `dev` stage.

### Docker Engine

All of the traditional `docker` commands accept a `--target` flag. For example, to build the `dev` stage directly, you could run `docker build --target dev -t backend:latest .`.

## Watch Out

* Stages are not required to copy or build on each other, they just often do
* Later stages will always be built after earlier stages, even if they don't explicitly depend on them.

## Additional Resources

| Resource | Description |
| --- | --- |
| [Docker: Multi-stage Builds](https://docs.docker.com/develop/develop-images/multistage-build/) | Official guide to multi-stage builds |
