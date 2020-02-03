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

```bash
$ docker-compose stop
$ docker stop $(docker ps -a -q)
$ docker-compose up
```

### Snafu'er than Snafu ☠️
If everything is still failing and you're close to insanity, you can try this collection of hardcore methods. Be prepared to lose all of your (database) data though:
```bash
$ docker-compose down
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

This won't update the `FROM` images in the custom Dockerfiles. You have pull them manually:

```
$ docker pull image-name
```

## Elastic

Elastic search containers use a lot of memory. Something with Java... Running multiple of them will hang Docker of your computer. Prevent multiple Elastic search containers from running symultatiosly. Stop the projects you're not working on. Or stop the Elastic container of your current project to reduce resource usage:

```
$ docker-compose stop elasticsearch kibana
```

Service name can differ, you can find them in `docker-compose.yml`.

## Node commands are choking on system dependencies
If you have done an `npm install` before on your host system (f.i. OSX), some binaries might not be compiled for the right system inside the web server (Debian). 

Solution is to compile again inside the container:
```bash
$ rm -rf node_modules
$ docker-compose exec web npm i
```

## MySQL error
```
--initialize specified but the data directory has files in it. Aborting.
```
Does the database tell you this during startup of your Docker image?
This is caused by a permission error on the mounted data volume. If this happens, please report it to us, with instructions on how to reproduce it.
As a workaround, you can delete `application/data/docker/*` and restart the server, which will empty the database and force MySQL to reinitialize.

## It works on my host, but not in Docker
Investigate the original image, take a look at the Dockerfile, what gets installed, etc. 

## A rogue volume is being very persistent
If, somehow, a volume won't let itself be deleted, because it's ostensibly "in use", but you know it isn't, you're in for a treat.
The volume won't be deleted with the `--force` flag, but try it anyway, as a first step:

```bash
$ docker volume rm --force my-volume
```

The solution is to delete the actual, physical directory that is the volume. `docker volume inspect` will you tell you its location:

```bash
$ docker volume inspect my-volume
```

Probably, something like `/var/lib/docker/volumes/my-volume/_data`. 
If you're on MacOS, here's the next hurdle: Docker runs Linux in a VM, and that path is not on your system, but on the VM.

Figure out the path to the VM, for example: `~/Library/Containers/com.docker.docker/Data/vms/0/`. In this folder is a file called `tty`, which you can use with `screen`, to log into the running VM.

```bash
$ screen /Users/harmen/Library/Containers/com.docker.docker/Data/vms/0/tty
```

From the screen-session, you can remove the volume directory:

```bash
$ rm -rf /var/lib/docker/volumes/my-volume/_data
```

Exit the screen session using `CTRL-A + CTRL-D`, and you're finally, finally free. Sweet release.

This can really trip you up when a corrupted volume is associated with a certain container, which will prevent you from starting the container and doing work.
