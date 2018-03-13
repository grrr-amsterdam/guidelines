## Pulling changes

Whenever you have local commits, but the remote also includes new work, you have diverged.

When doing a regular `git pull` you will be dropped into your editor to create a merge commit. 

- ABORT immediately. Clear out the commit message and quit your editor. 
- Use `git merge --abort` to discard your merge state.
- Use `git pull --rebase` instead to pull changes but avoid the noise of a merge commit.

## Lifecycle of a feature branch

- Start the feature: `git checkout -b my-feature`
- Do your commits.
- Rebase frequently to incorporate upstream changes.
  - `git fetch origin`
  - `git rebase origin/master`
- Push to the remote: `git push -u origin my-feature`
- Create a Pull Request on Github.
- When the PR has been approved, use interactive rebasing to clean up your history (see **Cleaning up your branch **below).
- Merge the PR and delete the branch, preferably using Github's **Rebase and Merge** option, in order to avoid the noise of a merge commit.

## Lifecycle of a hotfix branch

It's exactly the same as starting a feature, with the notable difference you will want to fork from some previously released version.

Let's say the current production version is tagged `1.50.4` and you've found a critical bug. 

- Create a hotfix branch, branched off the previous production version: `git checkout -b my-fix 1.50.4`
- Do your commits as usual.
- Push to the remote: `git push -u origin my-fix`
- Create a Pull Request on Github. Mention this is a hotfix.
- When the PR has been approved, tag your HEAD: `git tag 1.50.5`
- Merge the branch, either on Github or on the CLI, but make sure to use a regular merge, with a commit: `git merge my-fix`
- Push the tag: `git push origin --tags`
- If applicable, remove the remote branch: `git push origin :my-fix`
- Push `master`: `git push origin master`

You can now deploy the new tag to production.

## Rebasing

- You're working on a feature branch
- Meanwhile, master has been updated and you wish to include the changes in your branch
- Go back to master: `git checkout master`
- Pull master to make sure you're up-to-date: `git pull origin master`
- Return to the feature branch: `git checkout my-feature`
- `git rebase master`
- If there are conflicts, resolve them and use: 
  - `git add .` But don't commit!
  - `git rebase --continue`
- If there's a remote tracking your branch, use `git push --force-with-lease`. Only force-push if you're sure there's nobody else working on the same branch!
- Note: git will complain about being diverged everything you change history (whenever you amend a commit or rebase a branch). It will suggest you should pull then push, but DO NOT PULL. It's YOUR feature branch, you know nobody else has worked on it. If not, you shouldn't be changing history.

## Cleaning up your branch

💡 Pro-tip: grab this git alias to drop into interactive rebase starting at the first commit of your branch: [https://github.com/harmenjanssen/dotfiles/blob/master/gitconfig#L46](https://www.google.com/url?q=https%3A%2F%2Fgithub.com%2Fharmenjanssen%2Fdotfiles%2Fblob%2Fmaster%2Fgitconfig%23L46&sa=D&sntz=1&usg=AFQjCNHIM7wYxfEHyqlfOgI3KMeoIO-4lA)

For example `git rbi master` would find the commit where you branched off `master` and allows you to rebase all commits from that point on.

Git will drop you into your configured editor where it will list the commits within the specified range.

By default all will be prefixed with the word `pick`. If you would leave this alone and simply save and quit the file, everything would stay the same. 

You can, however, use various other keywords to change the history of your branch:

`pick` = use commit

`reword` = use commit, but edit the commit message

`edit` = use commit, but stop for amending

`squash` = use commit, but meld into previous commit

`fixup` = like "squash", but discard this commit's log message

`exec` = run command (the rest of the line) using shell

`drop` = remove commit

After saving the file Git will replay or drop your commits and/or smoosh 'm together like you instructed. When using `squash` or `reword` you will be prompted to come up with a new commit message.

Note: see the general advice above in the **Rebasing** section about force-pushing et cetera.

Note: you can swap the order of commits by simply reordering them in the rebase file.

Note: if you want to edit a commit message, you have to choose `reword`. Changing the commit message directly in the rebase file won't work. You will be prompted for a new message after saving the rebase file.

## Writing a good commit message

- Stick to a 72 character limit for your commit message
- Always start your message with one of the following imperatives: `update`, `fix`, `add` or `remove`.
- Separate the subject line from the body using a blank line
- End the message with a line containing only the ticket number whenever possible, like so: `AC-323` (in case of ActiveCollab).
- Use the body to elaborate: why was this change necessary? What subtasks have been done to facilitate this change?
- When applicable, mention the ticket number in the body of the commit. For instance, a commit referencing a feature request in ActiveCollab ticket #891 should mention `AC-891` somewhere in the body. Multiple commits can reference the same ticket number, and sometimes a single commit can reference multiple ticket (even though that feels suspiciously like trying to do a little too much work in a single commit!).

Aside from the commit message *format*, there are other rules to keep in mind:

- Avoid commits like "Fix whitespace", "Add documentation", "Add unit test", "Fix styles". If that's all there is to a commit, by all means describe it as such, but 9 times out of 10 these commits can be squashed into more descriptive commits.
- Just like when coding, adhere to the single responsibility principle. Make sure your commit does *one* thing well. If you've already made multiple changes and forgot to commit, simply use `git add --patch` to stage a subset of changes in a file. 
- Similarly, when committing changes that should've been part of a previous commit, use `git commit --fixup` and `git rebase -i` to quickly append your changes to any previous commit. See for more info on this technique: [https://robots.thoughtbot.com/autosquashing-git-commits](https://www.google.com/url?q=https%3A%2F%2Frobots.thoughtbot.com%2Fautosquashing-git-commits&sa=D&sntz=1&usg=AFQjCNEw94nfXJ5K92v8eenzZQkFUvMLlQ)

## Code review

We follow these guidelines as composed by Thoughtbot: [https://github.com/thoughtbot/guides/tree/master/code-review](https://www.google.com/url?q=https%3A%2F%2Fgithub.com%2Fthoughtbot%2Fguides%2Ftree%2Fmaster%2Fcode-review&sa=D&sntz=1&usg=AFQjCNEKhEQHkQFrbsnXflVPlOOPE-xbCA)