## Preliminary
⚠️This is an outtake of the [Garp3](https://github.com/grrr-amsterdam/garp3) wiki, where we explain how to use our helper tools with Git Flow.

We no longer recommend Git Flow but have instead moved to the simpler [One Flow](http://endoflineblog.com/oneflow-a-git-branching-model-and-workflow) model.

However, if you're using a Garp version below `3.13` you might benefit from the workflow and tools described below.


----------------------------------------------------------

## Git Flow

We use the [Succesful Git Branching Model](http://nvie.com/posts/a-successful-git-branching-model/) for storing and releasing our code.

Note that the following code samples use the `g` binary that's installed into `vendor/bin`.

## Start a feature
Use this when you're starting your own feature, or to store any work in progress (may be pushed back to Github if you want).

```
$ g feature start my-feature-name
```

## Finish the feature
Finish the branch when it's ready to be merged into the develop branch, and essentially to go live.

```
$ g feature finish
```
Finish the current feature branch.


## Start a release
These go live on staging before they are merged into master.

```
$ g release start [major]
```

Calling this without the *major* param (default) results in an increased *minor* semantic version number; the middle number in the triplet (n.**n**.n). When the *major* param is passed along, the first number is increased (**n**.n.n).

## Deploying a release

To deploy another branch than *master*, see [Deploying](http://code.grrr.nl/golem/wiki/deploying).


## Finish the release

```
$ g release finish
```

This finishes the release and merges it into master. Note that this is not necessary for live versions. Especially staging will often run a release branch instead of master.




## Start a hotfix

A hotfix branches off from master, and allows you to do some fixes that will be merged back into develop and master.

```
$ g hotfix start
```
This results in an increased 'patch' semantic version number; the last number in the triplet (n.n.**n**).

## Finish the hotfix

```
$ g hotfix finish
```
