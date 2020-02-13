# Docker

At GRRR, we use Docker for local development and more. This ensures development-production parity, and prevents a lot of manual configuration work.

- [OS Preparation](os-setup/README.md).
- [Project setup](project-setup/README.md).
- [⚠️ **Troubleshooting**](troubleshooting/README.md).
- [Running projects in Docker](running/README.md).

## Further information

Anatomy of a Docker command:

```sh
docker-compose -f docker-compose.testing.yml exec php php artisan clear:cache
```

- **docker-compose** You can run commands using `docker`, or using `docker-compose`. Usually you will use `docker-compose`, since that is what we use.
- **-f docker-compose.testing.yml** This is a flag passed to the `docker-compose` command. In this case we instruct it to use config file `docker-compose.testing.yml`. Usually optional, until you need something advanced.
- **exec php** This executes a command on a _running container_ named `php`. An alternative to `exec` is `run`, which is used to run a command on a container that is not running, such as `yarn build` on our conventional `builder` container. `php` is not a command, but the name of the container.
- **php artisan clear:cache** This is the command that you execute on the container. It's a PHP command, executed in this case on a container that is also named `php`.
