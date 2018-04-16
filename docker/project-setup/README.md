# Docker Project setup

- Have your local `.env` file point to the Docker database host:
  `DB_HOST=db`
- To boost performance between container and host, add `docker-sync` as a dependency in the project's `Gemfile`:
  `gem 'docker-sync', '~> 0.5.7'`
- Install the dependency:
  `$ bundle install`
  (if you don't have Bundler, run `$ gem install bundler` first)



Make sure you have the following files in your project:

- `docker-compose.yml`
- `docker-compose-dev.yml`
- `docker-sync.yml`

@todo: we will author a generic scaffold for these files.
For now, you can copy them from a previous recent project.