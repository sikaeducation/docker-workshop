# Docker: Rails

Rails apps are notoriously difficult to set up with Docker. Some notes:

* There is no Rails-specific Docker image. Rails apps should be configured off of the official Ruby images.
* Since Rails relies so heavily on scaffolding and generators, you need to ensure that Rails commands are being run by the host user rather the root user of the container
* The Rails image you make will need to be rebuilt every time you add or remove gems
* Use `sudo` any time you rebuild your images
* Rails' database connection must be pointed to the database service
* Use multi-stage Docker builds to separate out the dependencies for scaffolding and existing apps
* Use two separate Docker Compose files for scaffolding and the app (rather than extending the same file)

## Starting a New Rails App

In your project directory, create an empty `app` folder to mount as a volume for your container. This is important to ensure that your host user retains ownership of the contents of the volume.

### Environment

Create an `.env` file with `USER_ID` and `GROUP_ID` variables that correspond to your host user's Unix user and group IDs. Remember to `.gitignore` this! You can use the following script to generate the correct IDs:

```bash
echo "USER_ID=`id -u`\nGROUP_ID=`id -g`" >> .env
```

### Dockerfile

Add the following contents to a `Dockerfile` in your home directory:

```docker
FROM ruby:3.0.3 as base

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
RUN apt-get update && apt-get install -y \
  nodejs \
  yarn \
  sudo

ARG USER_ID
ENV USER_ID $USER_ID
ARG GROUP_ID
ENV GROUP_ID $GROUP_ID

ARG USER=ruby
ENV USER $USER

RUN groupadd -g $GROUP_ID $USER && \
    useradd -u $USER_ID -g $USER -m $USER && \
    usermod -p "*" $USER && \
    usermod -aG sudo $USER && \
    echo "$USER ALL=NOPASSWD: ALL" >> /etc/sudoers.d/50-$USER

WORKDIR /app
USER $USER
RUN gem install bundler

# development stage
FROM base as dev
COPY ./app/Gemfile* ./
RUN bundle install
CMD [rails, s, -b, "0.0.0.0"]
```

* Note that `nodejs` and `yarn` are only required if Rails is being used for front-end development.
* The user management section accepts a user ID and a group ID from the host system, and then creates a user called `ruby` in the container with the same IDs. This is necessary so that the files Rails scaffolds with commands like `new` and `generate` are owned by your host user, rather than root. This makes them editable on the host with your text editor.
* The development stage is only used after the app has been scaffolded out.

### New Compose File

Make a specific Compose file just for the Rails scaffolding called `docker-compose.new.yml`:

```yaml
services:
  rails:
    build:
      context: .
      args:
        USER_ID: $USER_ID
        GROUP_ID: $GROUP_ID
      target: base
    user: ruby
    volumes:
      - ./app:/app
```

* This targets the `base` build of the Dockerfile.
* The `rails` service passes the `USER_ID` and `GROUP_ID` environment variables from the host environment into the image. `docker-compose` automatically loads the variables from an `.env` file in the same folder.
* `ruby` is the new user that was created by the container.

### Scaffolding a New Rails App

To scaffold a new Rails app:

1. With the `Dockerfile`, `docker-compose.new.yml`, and `.env` files filled out, run `docker-compose -f docker-compose.new.yml build` to build the generation image.
2. Open a shell in the `rails` service with `docker-compose -f docker-compose.new.yml run rails /bin/bash`
3. Run `bundle init` and `bundle add rails` to download Rails
4. Run `bundle exec rails new . -d postgresql`
  * Always prefix Rails commands with `bundle exec` to use the project version of Rails rather than a system version
  * When prompted, overwrite your existing Gemfile
  * Note that the installs will happen, but only in the ephemeral instance of the container rather than the built image. The real advantage to this is that the Gemfile and all the folders are being created inside the mounted volume, which will persist
5. Make the `Gemfile` and `Gemfile.lock` files created by the `bundle install` writable with `chmod a+w Gemfile*`
6. Exit the container shell with `exit`

## Integrating an Existing App

### Docker Compose

Create the following `docker-compose.yml` in the root of the project directory:

```yaml
services:
  rails:
    build:
      context: .
      args:
        USER_ID: $USER_ID
        GROUP_ID: $GROUP_ID
      target: dev
    user: ruby
    ports:
      - 3000:3000
    volumes:
      - ./app:/app
    depends_on:
      - database
    command: [bundle, exec, rails, s, -b, '0.0.0.0']
  database:
    image: postgres:11
    volumes:
      - ./database:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=some_secure_password
```

* This targets the `dev` stage of the Dockerfile instead of the `base`.
* The `rails` service passes the `USER_ID` and `GROUP_ID` environment variables from the host environment into the image. `docker-compose` automatically loads the variables from an `.env` file in the same folder.
* `ruby` is the new user that was created by the container.
* The `rails` service needs to link to the `database` service by including it in `depends_on` to communicate with it.
* The `rails` service needs to bind the IP address `'0.0.0.0'` when starting the server for the site to be accessible outside the container on port 3000
* The `POSTGRES_PASSWORD` must be set for the database image to work, but the other Postgres values can use defaults

Build the image with `sudo docker-compose build` to install the dependencies.

### Connecting the Database

To connect a Postgres database to Rails, edit the `config/database.yml` file in the Rails folder:

```yaml
default: &default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  host: database
  username: postgres
  password: some_secure_password
  database: postgres
```

`postgres` is the default username and database name for Postgres Docker images, `some_secure_password` is whatever you set in the Docker Compose file. `host` should be set to the name of the database service specified in `docker-compose.yml`.

* Make sure you delete or update any `username`s, `password`s, and `database`s for the other environments or they'll override your default.
* You may want to put the password in an environment file with something like the `dotenv` gem
* You may want to create a separate testing database service to connect to in the `test` environment

## Running Generators

To run generator commands:

```sh
docker-compose run rails bundle exec rails generate model some-model-name
```

If you make new database migrations, you can run them with `docker-compose run rails bundle exec rails db:migrate`

### Installing Gems

To install a gem, either add it to the `Gemfile` or open a shell in the service and use `bundle add gem-name`. Then, rebuild the image with `sudo docker-compose build`.

## Running the Server

Just run `docker-compose up`! Any edits you make to local files on your host computer will be immediately reflected in the container.

## Watch Out!

* When other people clone your repo, they'll need to create an `.env` file with their `USER_ID` and `GROUP_ID` as well. The same script will work: `echo "USER_ID=``id -u``\nGROUP_ID=``id -g``" >> .env`. This may be worth adding to a saved shell script.
* `sudo docker-compose build` must be used because the Postgres image always claims root ownership of the volume you mount in it.
* To add gems, make sure that the Unix role `other` has write permissions to the `Gemfile` and `Gemfile.lock` files
* Remember to use `bundle exec rails` commands rather than the system `rails`.

## Additional Resources

| Resource | Description |
| --- | --- |
| [Docker Hub: Ruby](https://hub.docker.com/_/ruby) | Official Ruby images |
