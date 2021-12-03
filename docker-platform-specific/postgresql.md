# Docker: PostgreSQL

Postgres is relatively easy to Dockerize. A couple of things to note:

* These images can generally be referred to directly in Docker Compose files, there's usually little reason to make a standalone Dockerfile.
* PostgreSQL images have serveral parameters that can be set via environment variables, but the only one you usually need to set is `POSTGRES_PASSWORD`. 
* The default user in a PostgreSQL container is `postgres`, as is the default database name.
* The data in PostgreSQL container is stored in `/var/lib/postgresql/data`, which is where any volumes should be mounted.
* PostgreSQL uses port `5432` by default

```yaml
services:
  database:
    image: postgres:14.1-alpine
    environment:
      POSTGRES_PASSWORD: "some-password"
    volumes:
      - ./local-database-folder:/var/lib/postgresql/data
```

## Additional Resources

| Resource | Description |
| --- | --- |
| [Docker Hub: PostgreSQL](https://hub.docker.com/_/postgres) | Official PostgreSQL images |
