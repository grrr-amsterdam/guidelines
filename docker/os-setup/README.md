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


## The `dock` shortcut to run Docker commands
Install the shortcut below in your shell to shorten the syntax to run commands on your running `web` container.
Now, instead of:
```bash
$ docker exec -it myproject_web_1 [command]
```
You can run:
```bash
$ dock [command]
```
For instance:
```bash
$ dock ls
$ dock bash
$ dock g spawn
$ dock g snippet create
```



### Bash

For in your `~/.bash_profile`:
```bash
function dock {
    docker exec -it ${PWD##*/}_web_1 "$@"
}
```
### Fish shell
Add this function with `funced dock` and then save with `funcsave dock`.
```bash
function dock
    docker exec -it (basename $PWD)_web_1 $argv
end
```


## Add convenience hostname

- Optionally, you can add the Docker database host (we use `db` for convention) to your local nameserver, so you can address it with this name instead of an IP address.

  Add the following line to `/etc/hosts`:
  `127.0.0.1 db`

