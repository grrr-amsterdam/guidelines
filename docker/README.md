# Docker

At Grrr, we use Docker for local development and more. This ensures development-production parity, and prevents a lot of manual configuration work.


First, make sure you [prepared your OS](os-setup/README.md).
Then [set up your project](project-setup/README.md) to use our Docker configuration.

In case you run into problems, refer to the [troubleshooting chapter](troubleshooting/README.md).



## Start your project

- `cd` into to the project directory.
- Run `docker compose up`
- Now you can run the site on [localhost](http://localhost), optionally followed by a port number if you have defined one in `docker-compose.yml`.



## Stop your project

Use `CTRL + C` in a running Docker window to stop both.
