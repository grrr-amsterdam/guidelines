# Setting up Docker in your project

Create the following files, at the specified paths, to setup Docker in our preferred way:

- `docker-compose.yml`

(**note** that the project in question is The Ocean Cleanup, change names where applicable)

```
version: '3.7'

services:
  web:
    image: httpd:2.4-alpine
    volumes:
      - web-data:/usr/local/apache2/htdocs
      - ./docker/web/httpd.conf:/usr/local/apache2/conf/httpd.conf
    labels:
      - traefik.http.routers.my-container.rule=Host(`theoceancleanup.localhost`)
    depends_on:
      - php

  php:
    build:
      context: docker/php
    volumes:
      - codebase:/var/www/html
    depends_on:
      - db
    entrypoint:
      - /bin/sh
      - -c
      - nohup /var/www/html/docker/php/add-reverse-proxy-host.sh web theoceancleanup.localhost && php-fpm

  db:
    image: mysql:5.7
    volumes:
      - db-data:/var/lib/mysql
    environment:
       MYSQL_ROOT_PASSWORD: secret
       MYSQL_DATABASE: my_database
       MYSQL_USER: my_user
       MYSQL_PASSWORD: my_password

  router:
    image: traefik:v2.0-alpine
    ports:
      - "80:80"
      - "8080:8080"
    volumes:
      - ./docker/traefik/traefik.yml:/etc/traefik/traefik.yml
      - /var/run/docker.sock:/var/run/docker.sock

  builder:
    build:
      context: docker/builder
    volumes:
      - ./web/app/themes/toc:/theme
      - node_modules:/theme/node_modules
    working_dir: /theme

  deploy:
    build:
      context: docker/deploy
    volumes:
      - codebase:/app
      - ~/.ssh:/root/.ssh
      - node_modules:/app/web/app/themes/toc/node_modules
    environment:
      - "USER=${USER}"
    working_dir: /app

volumes:
  db-data: {}
  node_modules: {}
  web-data:
    driver: local
    driver_opts:
      type: nfs
      o: addr=host.docker.internal,rw,nolock,hard,nointr,nfsvers=3
      device: ":$PWD/web"
  codebase:
    driver: local
    driver_opts:
      type: nfs
      o: addr=host.docker.internal,rw,nolock,hard,nointr,nfsvers=3
      device: ":$PWD"
```

Note that some of these paths are project-specific (WordPress in this case). `web/app/themes` for instance would contain your front-end assets and is therefore the domain of your `builder` service.
Adjust the path to match your project. 

## `./docker/traefik/traefik.yml`

```yml
# Docker configuration backend
providers:
  docker:
    defaultRule: "Host(`{{ trimPrefix `/` .Name }}.localhost`)"

# API and dashboard configuration
api:
  insecure: true

global:
  sendAnonymousUsage: false
```

## `./docker/builder/Dockerfile`

```Dockerfile
FROM node:12-alpine

RUN apk add --no-cache \
    autoconf \
    zlib \
    build-base \
    nasm
```

## `./docker/deploy/Dockerfile`

```Dockerfile
FROM node:12-alpine

ENV RUBY_DEPS ruby ruby-bundler ruby-dev
ENV CAP_DEPS git openssh
ENV USER $USER

RUN apk add --no-cache \
    autoconf \
    zlib \
    build-base \
    nasm \
    $RUBY_DEPS \
    $CAP_DEPS \
    # Configure Ruby and Bundler
    && echo "gem: --no-document" > ~/.gemrc \
    && bundle config --global silence_root_warning 1
```

## `./docker/php/Dockerfile`

```Dockerfile
FROM php:7.1-fpm-alpine

ENV GD_DEPS freetype-dev jpeg-dev libpng-dev
ENV INTL_DEPS icu-dev
ENV MEMCACHED_DEPS libmemcached-dev zlib-dev
ENV MUSL_LOCALE_DEPS cmake make musl-dev gcc gettext-dev libintl
ENV MUSL_LOCPATH /usr/share/i18n/locales/musl
ENV ZIP_DEPS libzip-dev

RUN apk add --no-cache \
    git \
    make \
    $GD_DEPS \
    $INTL_DEPS \
    $MEMCACHED_DEPS \
    $MUSL_LOCALE_DEPS \
    $ZIP_DEPS \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/bin --with-jpeg-dir=/usr/bin --with-png-dir=/usr/bin \
    && pecl install memcached-3.1.3 \
    && docker-php-ext-install -j$(nproc) gd pdo pdo_mysql zip \
    && docker-php-ext-enable memcached \
    # Install musl-locales for locales support
    && wget "https://gitlab.com/rilian-la-te/musl-locales/-/archive/master/musl-locales-master.zip" && unzip musl-locales-master.zip && \
    	cd musl-locales-master && cmake -DLOCALE_PROFILE=OFF -DCMAKE_INSTALL_PREFIX:PATH=/usr . && make && make install && \
    	cd .. && rm -r musl-locales-master \
    # Symlink locale so Garp_Application_Resource_Router can find it
    && ln -s $MUSL_LOCPATH/nl_NL.UTF-8 $MUSL_LOCPATH/nl_NL.utf8

ADD add-reverse-proxy-host.sh docker/php/

ENV COMPOSER_ALLOW_SUPERUSER 1
COPY --from=composer /usr/bin/composer /usr/bin/composer

ENV PATH $PATH:./vendor/bin
```

## `add-reverse-proxy-host.sh`

```Shell
#!/bin/sh
# @author       David Spreekmeester <david@grrr.nl>
# @description  Solaris shell script to add a mock localhost entry to a container,
#               enabling it to reach the proxy container by the same hostname
#               as you would use from your host as a developer.

# Provide this script with 2 arguments:
# 1. Name of the Docker service, f.i. 'web'
REV_PROXY_SERVICE=$1
# 2. Domain that you will address this service by, f.i. 'poobar.localhost'
REV_PROXY_DOMAIN=$2
[ -z "$1" ] && echo 'Missing arg 1:' && \
    echo 'Provide the Docker service that functions as proxy, f.i. "web"' && exit 1
[ -z "$2" ] && echo 'Missing arg 2:' && \
    echo 'Provide the hostname you use to address the proxy, f.i. "poobar.localhost"' && exit 1

# Wait for Docker to provision the containers with internally mapped `hosts` files.
sleep 5
NAMESERVER=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}')
NSLOOKUP="$(nslookup ${REV_PROXY_SERVICE} ${NAMESERVER})"
REV_PROXY_IP=$(printf "${NSLOOKUP}" | grep 'Address 1' | grep ${REV_PROXY_SERVICE} | awk '{print $3'})

[ -z "$REV_PROXY_IP" ] && echo "Unable to find '${REV_PROXY_SERVICE}'. Provide a valid container name." && exit 1

echo "${REV_PROXY_IP} ${REV_PROXY_DOMAIN}" >> /etc/hosts
```

[TO DO]

- httpd.conf?
