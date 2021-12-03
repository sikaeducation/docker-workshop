# Docker: Image Layers

Each `RUN` and `COPY` command in a Dockerfile creates a new layer in the Docker image that sits on top of the layers that came before it. This is significant because each layer comes with some overhead and contributes to the size of the final image. When a command is changed in the Dockerfile or the files from a Dockerfile command like `COPY` change, every affected layer as well as every layer after them must be rebuilt. This means that the order of commands in Dockerfiles can significantly impact how long it takes to rebuild images when changes happen.

## Guidelines

* In general, instructions in Dockerfiles should be ordered from least changing to most changing.
* Most official Docker images have a Debian variant (indicated by the version of Debian, such as `buster` or `bullseye`), a `slim` Debian variant that strips out a lot of utilities, and an `alpine` variant that's designed to be as small as possible. Larger variants are easier to develop with, while smaller images have fewer tools installed and thus may not be able to do things like use `apt` or compile dependencies.
* Each Docker image should only do one thing and only contain the files and programs needed to do that thing. For example, a database image doesn't need a text editor.
* Whenever possible, combine `RUN` commands by chaining them with `&&`
  * In particular, Linux dependencies are typically installed with `apt update && apt install -y list of package names go here`. `apt update` and `apt install` should always be chained into the same `RUN` to prevent caching issues.
* Put long commands such as `RUN` chains or commands with multiple arguments on their own lines by ending each line with `\`
* Unlike `RUN` commands, `COPY` commands should generally be split up based on how often the files change. For example, adding dependency files like `package.json` separately from the rest of the app means that this layer will only need to be rebuilt if dependencies change.
* Change the `WORKDIR` instead of chaining `cd` commands in `RUN` statements
* Expose the traditional ports for each application, such as `80` for HTTP, `5432` for PostgreSQL, `3306` for MySQL, etc. They can be rebound to different ports in the host as needed.
* If a file should not be included in commands like `COPY` such as log or temp files, you can exclude them with `.dockerignore`. Each file path or pattern will be ignored by Docker, similar to `.gitignore` files.
* `WORKDIR`, `EXPOSE`, `USER`

## Watch Out!

* Older versions of Docker made new layers for every single instruction in the Dockerfile. Modern versions only create new layers for `RUN`, `ADD`, and `COPY` instructions.
* `COPY` and `ADD` fundamentally do the same thing, but `COPY` can only copy local files, while `ADD` also supports remote URLs and importing archives. Prefer `COPY` for standard usage.

## Additional Resources

| Resource | Description |
| --- | --- |
| [Best Practices For Writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) | Official guidelines for Dockerfiles |
