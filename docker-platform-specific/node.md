# Docker: Node

Node is one of the easiest platforms to Dockerize since all of its dependencies are stored in the project folder.

A sample Docker Compose file:

```yaml
services:
  api:
    image: node:latest
    volumes:
      - ./app:/app
    ports:
      - "3000:80"
    command: [npm, start]
```

Some common optimizations for Node apps:

```dockerfile
FROM node:16.13-alpine3.12
WORKDIR /app

COPY package*.json ./
COPY node_modules .
RUN npm ci

COPY --chown=node:node . .

USER node
EXPOSE 80
CMD [npm, start]
```

* Node does especially well with small Docker base images like Alpine
* Copy dependencies separately from the app code to take advantage of layer caching
* `npm ci` installs directly from the `package-lock.json` file instead of the `package.json` file for better reproducibility
* Use an unprivileged user (such as `node`) to actually run the application

## `.dockerignore`

Add these files to a `.dockerignore` file to prevent them from getting clobbered:

```
node_modules
npm-debug.log
```

## Running Node Commands

Run node commands with `docker-compose run`:

```bash
docker-compose run api npm install lodash
```

## Additional Resources

| Resource | Description |
| --- | --- |
| [Docker Hub: Node](https://hub.docker.com/_/node) | Official Node images |
