# Docker: `wait-for-it.sh`

Docker can wait for a container to start, but doesn't have any way for telling whether or not a container is actually ready to receive requests. In most development or production environments, this usually causes problems for a couple seconds at most. In testing environemnts however, that can lead to a test failing because it thinks a service is up but isn't.

The solution is the `wait-for-it.sh` script. Download the script to the project directory, and then have that run your actual start script. 

## Installing

`wait-for-it.sh` is just a file. You can download it to your current directory using the following command:

```bash
curl -O https://raw.githubusercontent.com/vishnubob/wait-for-it/master/wait-for-it.sh
```

## Using

This `docker-compose.yml` file has the `frontend` service waiting for the `backend` service to start, but doesn't ensure that the backend is actually ready to receive requests before starting:

```js
services:
  backend:
    build: .
    expose:
      - 3000
    command: [backend/entrypoint.sh]
  frontend:
    build: .
    depends_on: backend
    command: [frontend/entrypoint.sh]
```

To wait for the `backend` service to start, change the `command` to:

```js
services:
  backend:
    build: .
    expose:
      - 3000
    command: [backend/entrypoint.sh]
  frontend:
    build: .
    depends_on: backend
    command: [./wait-for-it.sh, backend:3000, --, frontend/entrypoint.sh]
```

## Watch Out!

* The default timeout for `wait-for-it.sh` is 15 seconds. This can be changed by setting the `-t` flag, eg. `[./wait-for-it.sh, -t, 30, backend:3000, --. frontend/entrypoint.sh]` waits for up to 30 seconds for the backend to become available.
* Note the `--` in the `wait-for-it.sh` API; it's what separates options that can be passed to `wait-for-it.sh` from the actual command that you want to run once the service is available

## Additional Resources

| Resource | Description |
| --- | --- |
| [wait-for-it.sh](https://github.com/vishnubob/wait-for-it) | Official code and documentation on `wait-for-it.sh` |
