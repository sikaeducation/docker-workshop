# Docker: Python

Python apps are relatively easy to Dockerize. They're similar to Node apps, although their dependencies are installed globally.

A sample Docker Compose file:

```yaml
services:
  python:
    image: python:latest
    volumes:
      - ./:/app
    working_dir: /app
    command: [python, app.py]
```

Some common optimizations for Python apps:

```dockerfile
FROM python:3.10.0
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD [python, app.py]
```

* Simple Python scripts without dependencies do well with small Docker base images like Alpine. If you need libraries, use a full version.
* Copy dependencies separately from the app code to take advantage of layer caching
* `--no-cache-dir` helps keep the image size smaller by not attempting to cache dependencies

## Installing Dependencies

1. Run the installation command in the container with something like `docker-compose run python sh -c "pip install pandas && pip freeze >> requirements.txt"`. This will do the installation in the ephemeral container, but will add the dependencies to the `requirements.txt` file in the persistent volume.
2. Rebuild the container to add the dependencies to the image

## Running Python Commands

Run Python commands with `docker-compose run`:

```bash
docker-compose run python python app.py
```

If it's a single-purpose script, you can also run it with `docker-compose up`.

## Additional Resources

| Resource | Description |
| --- | --- |
| [Docker Hub: Python](https://hub.docker.com/_/python) | Official Python images |
