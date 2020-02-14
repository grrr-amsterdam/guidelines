# Docker Project setup

- [Back to overview](../README.md).
- [OS Preparation](../os-setup/README.md).
- [Project setup](../project-setup/README.md).
- [⚠️ **Troubleshooting**](../troubleshooting/README.md).
- [Running projects in Docker](../running/README.md).


Have your local `.env` file point to the Docker database host:
  `DB_HOST=db`


Make sure you have the following files in your project:

- `docker-compose.yml`
- `docker-compose.override.yml` (optional)

## Speedup Composer install

Composer uses caching to speed up install and update commands, but with Docker the cache stays in the container. The next time you run Composer install the cache will be gone.

By making the cache directory available in all your `php` containers Composer doesn't have to download the same packages every time.

```docker
# docker-compose.override.yml
version: '3.7'
services:
  php:
    volumes:
      - ~/.composer:/root/.composer
```
