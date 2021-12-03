Rewrite this Dockerfile to be as efficient as possible:

```dockerfile
FROM node:latest
EXPOSE 80
WORKDIR /app

COPY ./ /app

RUN apt-get -y update
RUN apt-get install -y git vim
RUN apt-get install -y npm nodejs

RUN npm install

CMD npm start
```

---

In which order would you place the following activities:

* Setting a non-root user
* Copying application files
* Installing app dependencies
* Setting the base image
* Setting the default command
* Installing system dependencies
* Exposing ports
