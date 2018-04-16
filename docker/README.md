# Docker

At Grrr, we use Docker for local development and more. This ensures development-production parity, and prevents a lot of manual configuration work.

This file is written for MacOS, but with some minor changes it should work for Linux and Windows as well.
We use Unison for high performance syncing, which is compatible with MacOS, Linux and Windows.



First, make sure you [prepared your OS](os-setup/README.md).
Then [set up your project](project-setup/README.md) to use our Docker configuration.

In case you run into problems, refer to the [troubleshooting chapter](troubleshooting/README.md).



## Start your project

- `cd` into to the project directory.
- Run `docker-sync-stack start`
  This will start `docker-sync` and fire up the Docker Compose cluster, returning the log output of all containers (both database and web server) directly into your terminal.
- Now you can run the site on [localhost](http://localhost), optionally followed by a port number if you have defined one in `docker-compose.yml`.



## Stop your project

Use `CTRL + C` in a running Docker / Docker-Sync window to stop both.
