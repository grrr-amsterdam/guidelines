# Docker OS Setup

## Installation

- Install the [Docker client](https://www.docker.com/community-edition#/download)


## NFS on MacOS
We use NFS to speed up file synchronization between host and container.
If you're running MacOS, NFS is not as fast as you'd like, out of the box.

You can use the following tweak to speed it up a little.

Add the following lines to `/etc/nfs.conf`:
```
nfs.client.allow_async = 1
nfs.client.nfsiod_thread_max = 128
```


## The `dce` shortcut to run Docker commands
Install the shortcut below in your shell to shorten the syntax to run commands on your running containers.
Now, instead of:
```bash
$ docker-compose exec web [command]
```
You can run:
```bash
$ dce web [command]
```
For instance:
```bash
$ dce web ls
$ dce web bash
$ dce web g spawn
$ dce web g snippet create
$ dce web phpunit
$ dce db mysql -uroot -p
```



### Bash

For in your `~/.bash_profile`:
```bash
function dce {
    docker-compose exec "$@"
}
```
### Fish shell
Add this function with `funced dce` and then save with `funcsave dce`.
```bash
function dce
    docker-compose exec $argv
end
```


## Add convenience hostname

- Optionally, you can add the Docker database host (we use `db` for convention) to your local nameserver, so you can address it with this name instead of an IP address.

  Add the following line to `/etc/hosts`:
  `127.0.0.1 db`

