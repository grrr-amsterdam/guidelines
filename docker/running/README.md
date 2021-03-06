# Running projects in Docker

- [Back to overview](../README.md).
- [OS Preparation](../os-setup/README.md).
- [Project setup](../project-setup/README.md).
- [⚠️ **Troubleshooting**](../troubleshooting/README.md).
- [Running projects in Docker](../running/README.md).

## Quickstart

- `cd` into to the project directory.
- Run `docker-compose up`
- Now you can run the site on {projectname}.localhost` for Traefik-enabled projects, or simply `localhost` on a specific port for older projects.
  
## Overview of services

The following is an overview of the containers used in our preferred setup. Note that this might not yet be available in _all_ projects.

(the example commands below assume you've added [these conventional shortcuts](../os-setup/README.md#the-dce-and-dcr-shortcuts-to-run-docker-commands))

Conventionally, these are the services in use:

- **web** - The Apache server (connected to FPM for PHP).
- **php** - Container running FPM. Execute your PHP commands here. Ships with Composer.
  - Example commands you would run here:
    - `$ dce php phpunit`
    - `$ dce php php artisan`
    - `$ dce php composer install`
- **db** - Container running the MySQL database. Use this to execute queries.
  - Example commands you would run here:
    - `$ dce db mysql -uroot -psecret my_database`
    - `$ dce db mysqldump -uroot -psecret my_database > my_data.sql`
- **router** - A [Traefik](https://containo.us/traefik/)-running container that provides routing for your app. We configure this router to enable {projectname}.localhost. It also provides a neat dashboard with an overview of running containers at [localhost:8080](http://localhost:8080). Be aware that this container binds to port 80 so can only run one container.
- **builder** - A container running node and yarn, used to build your front-end assets.
  - Example commands you would run here:
    - `$ dcr builder yarn install`
    - `$ dcr builder yarn watch`
  - _Note that since this is not a running container, you use `docker-compose run` instead of `docker-compose exec`!_
- **deploy** - A container running ruby, node and capistrano, used to deploy your project.
  - Example commands you would run here:
    - `$ dcr deploy cap staging deploy`
  - _Note that since this is not a running container, you use `docker-compose run` instead of `docker-compose exec`!_


[See an example docker-compose.yml file and learn how to setup your project this way](../docker-setup)

## Detached mode

To run the containers in the background you can use the `-d` flag: `docker-compose up -d`.

Follow the logs of the php container run `docker-compose logs -f php`. To follow the logs of all the containers omit the container name.

## Stop your project

Use `CTRL + C` in a running Docker window to stop both.

When running in detached mode do `docker-compose stop` in your project folder. This won't throw away the containers. To do that run `docker-compose down`, but stop is usually what you want.

## Update images

When Docker Compose can't find an image it will download it. But after that it won't fetch updates. To pull the latest images run:

```bash
$ docker-compose pull
# and restart
$ docker-compose stop && docker-compose up
```

But this won't pull images for services that use a `Dockerfile`. You have to manually pull those updates. Look for image name and version in the `Dockerfile` of the service you want to update. There will always be one or more `FROM` lines, but `COPY` can also contain an image name.

A list of commonly used images, but always check the `Dockerfile` because binary and Alpine version can vary:

```bash
# Builder service
$ docker pull node:10-alpine
# Deploy service
$ docker pull node:12-alpine
# PHP service
$ docker pull php:7.1-fpm-alpine
$ docker pull composer
```

To use the pulled images you have to rebuild your app images:

```bash
$ docker-compose build
# and restart
$ docker-compose stop && docker-compose up
```
