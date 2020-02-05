# Dependencies you need locally
This is everything you need on a Mac to do development work at GRRR.


## SSH key
Create an SSH key:
```bash
$ ssh-keygen
```

Load it in your `~/.bash_profile` or alike:
```bash
ssh-add -K ~/.ssh/id_rsa > /dev/null 2>&1
```

Source it to activate in the current session:
```bash
$ source ~/.bash_profile
```

Copy the public key to your clipboard:
```
$ pbcopy < ~/.ssh/id_rsa.pub
```

Now insert that key into Github:
* Click your profile picture, at the right top
* Go to _Settings_
* Go to _SSH and GPG keys_ in the menu on the left
* Create a _New SSH key_ and paste the contents there.


## Homebrew
What would we do without it?
Not a whole lot, so go on and install it like there's no tomorrow:

```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```


## Sudo-less Gems
Starting from MacOS High Sierra, you need a home for your gems.
Otherwise you might need `sudo` to install gems, and you don't want that.
Put this in your `~/.bash_profile` or alike:

```bash
export GEM_HOME=~/.gem
export PATH=$PATH:$GEM_HOME/bin
```

Now you can just use `gem install` without extra permissions.
You should never need `sudo` for this!


## Node and Composer paths
Make sure locally (project scoped) modules always take precedent by putting this in your `~/.bash_profile`:

```bash
export PATH=./vendor/bin:./node_modules/.bin:$PATH
```


## Docker and friends
Let's get Docker from Brew:
```bash
$ brew install caskroom/cask/brew-cask
$ brew cask install docker
```


## Application context for Garp 3
Garp 3 projects require this env var, so set it in your `~/.bash_profile`:
```bash
export APPLICATION_ENV=development
```


## Node version
Some projects support Node v8 some v10. The supported version is defined in `package.json`.

[nvm](https://github.com/nvm-sh/nvm) is a Node version manager. After [installation](https://github.com/nvm-sh/nvm#install--update-script) you can use the `nvm` command like this:

```bash
$ nvm use 8
```

Switch Node version with Brew:

```bash
$ brew install node@8
$ brew unlink node
$ brew link node@8 --force --overwrite
```
