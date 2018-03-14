# Deployment

## Fully Automatic

We prefer automatic deployments for both `staging` and `production` environments.

In this scenario, any addition to the `master` branch in Git is tested and deployed to the `staging` environment.

The `production` environment requires a manual action to make sure that we only release versions to the environment which are fully ready to be used by the general public.

This works by first adding a tag to the commit, indicating the current version:

```bash
$ git tag 1.2.3
```

After this version is tested on the `staging` environment, and there is permission to release it to `production`, we add a second tag:

```bash
$ git tag 1.2.3-release 1.2.3
```

Here, another tag (`1.2.3-release`) is added to the same commit that was already tagged with `1.2.3`.

We use `-release` as a convention to indicate to Travis that this version should be deployed to `production`. The auto-deploy mechanism will run all tests again and if successful, the latest version will be live shortly thereafter.

### Failed deploys

Should the automatic deploy to `production` fail for some reason, it's advisable to use the "Restart Build" functionality in Travis.



## Semi-Automatic

Older projects require a manual deploy to `production`:

```bash
$ sh deploy.sh production
```

This runs the `deploy.sh` script that you will find in the root of the project, making sure all dependencies are installed.



## Manually

In even older projects, you might not have a `deploy.sh` file. In that case, a deploy by Capistrano is necessary:

```bash
$ cap deploy staging
$ cap deploy production
```

