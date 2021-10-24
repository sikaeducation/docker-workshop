## Using Docker Compose

The basic configuration of Docker Compose services includes pointing to images, mounting volumes, exposing ports, and setting default commands. In practice, several other configuration options, commands, and techniques are useful for managing apps with Docker Compose.

* [Using Custom Images](#Using-Custom-Images)
* [Internal Ports](#Internal-Ports)
* [Entrypoints and Commands](#Entrypoints-and-Commands)
* [Environment Variables](#Environment-Variables)
* [Multiple Environments](#Multiple-Environments)
* [Running Commands as the Host User](#Running-Commands-as-the-Host-User)

## Using Custom Images

If an image is officially supported by Docker Hub, you can point to it using the `image:tag` format. To point to an image you or someone else has saved on DockerHub, use the `username/image:tag` format:

```yaml
services:
  backend:
    image: kylecoberly/rails:2.7
```

If the Dockerfile for a service is saved as part of the codebase, use the `context` property of the `build` property:

```yaml
services:
  backend:
    build:
      - context: ./backend
```

This should be a path to the folder containing the `Dockerfile`, relative to the location of the `docker-compose.yml` file.

## Internal Ports

If the port only needs to be visible to other services, use `expose` instead of `port`:

```yaml
services:
  backend:
    expose:
      - "443"
  database:
    expose:
      - "5432"
  frontend:
    port:
      - "8000:443"
```

Within your Docker environment, these services will available by their name. For example, your front-end should might make API calls to your backend at `https://backend:443`, and your backend might connect to your database at `postgres://database:5432`. The front-end will be available on the host at `http://localhost:8000`, and neither your backend nor your database will be directly accessible through the host.

### Entrypoints and Commands

There are two properties in Docker Compose files and Dockerfiles that do similar things, but are easily confused. An `entrypoint` is either a command or start of a command that is generally not intended to be overridden. For example, if you have a node server and the only command you every want to be able to run with it is `npm start`, that should be an `entrypoint`:

```yaml
services:
  backend:
    entrypoint: ["npm", "start"]
```

Running `docker-compose up` will start the `backend` service with `npm start`.

Alternatively, if you want to be able to support both `npm start` and `npm test`, you could use `command`, which is intended to have a default which can be overridden.

```yaml
services:
  backend:
    command: ["npm", "start"]
```

Running `docker-compose up` will start the `backend` service with `npm start`, while running `docker-compose up backend npm test` will run the tests. It's possible to use both, for example making `npm` the entrypoint, `start` the default command, and `test` or other scripts as overrides.

```yaml
services:
  backend:
    entrypoint: ["npm"]
    command: ["start"]
```

```bash
docker-compose up # runs `npm start`
docker-compose up backend test # runs `npm test`
docker-compose up backend run my-cool-script # runs `npm run my-cool-script`
```

### Environment Variables

There are a couple of ways to use environment variables in Docker Compose.

```yaml
services:
  backend:
    env_file: .env
    environment:
      - SOME_KEY=SOME_VALUE
```

Note that each environment variable (key _and_ value) is a single string, not a YAML collection

File paths are relative to the location of the `docker-compose.yml` file. Values specified in the `environment` will always clobber values pulled from an `env_file`. Environment variables in environment files are expected to be in `KEY=VALUE` format, one per line.

Note that these values will be available as environment variables to the service at runtime, not when the container is built. To specify environment variables for building the container, use `build`/`args`:

```yaml
services
  backend:
    build:
      context: ./path/to/Dockerfile
      args:
        - NODE_ENV: development
```

## Multiple Environments

It's common to need slightly different configurations in different environments, such as one that you use in development and another one you use in production. You can selectively override parts of Docker Compose config files by using the `-f` option:

```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml up
```

The configuration values in later files will clobber the values in earlier files. Subsequent files don't actually need to be complete config files, they only need to contain the values you wish to override.

As these commands get long and unwieldy, they can be saved in shell scripts.

## Running Commands as the Host User

Most Docker containers run all of their commands as root. This is a problem if they create new files in your volume, as you'll be unable to edit them as your regular user. The solution is to tell `docker-compose` to run as your user:

```bash
docker-compose run --user `id -u`:`id -g` -w /app api npm init
```

* `--user` (also `-u`) sets the user the container should use
* ```id -u``` returns the ID of the user on your host machine, ```id -g``` return the ID of your user group. `id -u`:`id -g` will evaluate to something like `1001:1001`, and will give your user ownership of files created inside the container.
* `-w` sets the working directory to the location of your project
* `api` is the name of the service
* `npm init` is the command for initializing a new Node project

This is most useful for commands that create or modify files, such as initializers like `npm init`, dependency management commands like `npm install`, and scaffolding commands like `rails generate`.

## Watch Out!

* You can control the order that containers start in with `depends_on`, but this doesn't actually ensure that any particular service is ready to accept requests yet. To do that, you need to use a script like [wait-for-it](https://github.com/vishnubob/wait-for-it).
* `docker-compose exec` ignores the `entrypoint` and runs whatever command you give it.
* Running commands in a container as your host user may have some issues. Since your user isn't actually added to the container, it doesn't have a username or a home directory, which can create problems with commands like `gem install`.

## Additional Resources

| Resource | Description |
| --- | --- |
| [Docker Compose Overview](https://docs.docker.com/compose/) | Official docs for Docker Compose |
| [Docker Compose Reference](https://docs.docker.com/compose/compose-file/compose-file-v3/) | Reference for all available configuration values for Docker Compose files |
