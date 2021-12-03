# Docker: Static Web Content

To host static web content, use the `nginx` image:

```yaml
services:
  web:
    image: nginx:1.21.4
    volumes:
      - ./site:/usr/share/nginx/html
    ports:
      - "8080:80"
```

Note that in development, it may be easier to use `lite-server` for auto-refresh on change:

```yaml
services
  web:
    image: node:latest
    volumes:
      - ./site:/app
    working_dir: /app
    command: npx lite-server
```

## Additional Resources

| Resource | Description |
| --- | --- |
| [Docker Hub: Nginx](https://hub.docker.com/_/nginx) | Official Nginx images |
| [Docker Hub: Node](https://hub.docker.com/_/node) | Official Node images |
| [npm: `lite-server`](https://www.npmjs.com/package/lite-server) | Documentation on `lite-server` |
| [Nginx](https://nginx.org/en/) | Documentation on Nginx |
