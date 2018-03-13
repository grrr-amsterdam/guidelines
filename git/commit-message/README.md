# Writing a good commit message

## Message format

- Stick to a 72 character limit for your commit message
- Always start your message with one of the following imperatives: `Update`, `Fix`, `Add` or `Remove`.
- Separate the subject line from the body using a blank line
- End the message with a line containing only the ticket number whenever possible, like so: `AC-323` (in case of ActiveCollab).
- Use the body to elaborate: why was this change necessary? What subtasks have been done to facilitate this change?
- When applicable, mention the ticket number in the body of the commit. For instance, a commit referencing a feature request in ActiveCollab ticket #891 should mention `AC-891` somewhere in the body. Multiple commits can reference the same ticket number, and sometimes a single commit can reference multiple ticket (even though that feels suspiciously like trying to do a little too much work in a single commit!).



## General rules

Aside from the commit message *format*, there are other rules to keep in mind:

- Avoid commits like "Fix whitespace", "Add documentation", "Add unit test", "Fix styles". If that's all there is to a commit, by all means describe it as such, but 9 times out of 10 these commits can be squashed into more descriptive commits.
- Just like when coding, adhere to the single responsibility principle. Make sure your commit does *one* thing well. If you've already made multiple changes and forgot to commit, simply use `git add --patch` to stage a subset of changes in a file. 
- Similarly, when committing changes that should've been part of a previous commit, use `git commit --fixup` and `git rebase -i` to quickly append your changes to any previous commit. See for more info on this technique: [https://robots.thoughtbot.com/autosquashing-git-commits](https://www.google.com/url?q=https%3A%2F%2Frobots.thoughtbot.com%2Fautosquashing-git-commits&sa=D&sntz=1&usg=AFQjCNEw94nfXJ5K92v8eenzZQkFUvMLlQ)



## Examples

Fixing a bug:

```
Fix encoding issue in newsletter form validation

This fixes the issue that occurred whenever senior hax0rs tried filling out the newsletter form.
- Enforced encoding to UTF-8
- Stripped weird WordPerfect™ chars
- Censored dirty emoji, like eggplant

AC-395
```



Updating a dependency:

```
Update gizzle-muffle-keeper to 1.4.5

Due to a security concern, update to latest version was necessary.
- Updated related unit test to new format
- Added flexible version update

AC-729
```

