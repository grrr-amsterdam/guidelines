# Troubleshooting
## Port in use? Free the ports!
Make sure Apache / Nginx is not running on your local machine on port `80`.
The same goes for MySQL if it's running on port `3306` on `localhost`.
To stop those on a default OSX install:
```
$ sudo apachectl stop
$ mysql.server stop
```
Or if there's another Docker Compose instance running, `cd` to that project dir and shut down the cluster:
```bash
$ docker-compose stop
```

## Everything SNAFU
If a machine is stuck and won't shut down, try stopping all containers first.
Then restart the Docker Compose cluster. You shouldn't be losing any data this way.

_Remove the dollar signs for Fish shell._

```bash
$ docker-compose stop
$ docker stop $(docker ps -a -q)
$ docker-sync-stack start
```

### Snafu'er than Snafu ☠️
If everything is still failing and you're close to insanity, you can try this collection of hardcore methods. Be prepared to lose all of your (database) data though:
```bash
$ docker-compose stop
$ docker-sync-stack clean
$ docker stop $(docker ps -a -q)
$ docker rm -f $(docker ps -a -q)
$ docker rmi -f $(docker images -q)
$ docker system prune
```

## Old version of a Docker image
To enforce a pull of the latest used Docker images:
```
$ docker-compose pull
```

## Node is going cray-cray / `npm install` is choking!
If you have done an `npm install` before on your host system (f.i. OSX), these are not compiled for the right system inside the web server (Debian). 

Solution is to compile again inside the container:
```bash
$ rm -rf node_modules
$ dock npm i
```

## Can't reach host
* Is Docker running?
* Are you using the right IP address? In the Docker for Mac (beta / edge) client this is `localhost` by default, in the older Docker-machine client this is `192.168.99.100`.

## MySQL error
MySQL at it again 🙄
```
--initialize specified but the data directory has files in it. Aborting.
```
Does the database tell you this during startup of your Docker image?
This is caused by a permission error on the mounted data volume. If this happens, please report it to us, with instructions on how to reproduce it.
As a workaround, you can delete `application/data/docker/*` and restart the server, which will empty the database and force MySQL to reinitialize.

## It works on my host, but not in Docker
Something is happening and it seems to be specifically tied to the Docker setup?
To debug, you can take a look at the setup that we use for Docker.
It consists of 3 opensource images:
* the [web server](https://github.com/grrr-amsterdam/garp3-httpd-docker)
* the [database server](https://github.com/grrr-amsterdam/garp3-db-docker)
* and the [data storage container](https://github.com/grrr-amsterdam/garp3-data-docker)