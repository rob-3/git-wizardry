---
theme: default
background: https://source.unsplash.com/collection/94734566/1920x1080
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
drawings:
  persist: false
monaco: true
title: Git Wizardry
---

# Git Wizardry

Demystifying git history and advanced features of git

---
layout: center
---

# Please interrupt, ask questions, contribute your own knowledge, and otherwise engage in this talk!

I can talk for an hour about Git, but it's much more interesting if y'all
participate.

---
layout: fact
---

# Everyone uses Git

---
layout: center
---

# Why?

Because this is horrifying:

<img src="/assets/bad-naming3.png"/>

---
layout: image-right
image: /assets/revision.png
---

# Word Processors already figured this out...

---
layout: two-cols
---

# Mistakes in Prose

<span class="grid place-content-center h-100 text-5xl">
I've inserted a mistake into this paragraph for you to find. <br>Can you spot the<br> the error in this sentence?</span>

::right::

# Mistakes in Code

<img src="/assets/npm-error.png" />

---
layout: center
---

# Small changes can completely break code

---

# Commits
A commit is a snapshot you can vouch for.

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
	"branchOffset": 50
}
end
commit
commit
commit
commit
branch HEAD
```

<!--
- A commit is represented by a hash of the contents and the parent.
- Don't just commit and push at the end of the day.
- Any time the code is in a working state, commit.
- This could be as small as a typo or as large as a multi-file refactor.
- Idiomatic git starts with idiomatic commits.
-->

---
layout: center
---

# Working with the working tree

---
layout: center
---

# `git add`

Stage changes for commit in the index

```bash
$ git add somefile.txt
$ git add .
$ git add -p
```

---
layout: image
image: /assets/git-add.png
---

---
layout: center
---

# `git reset` (**NOT** `--hard`)

Unstage changes that have been added

```
$ git reset
```

---
layout: image
image: /assets/git-reset-2.png
---

---
layout: center
---

# `git stash`

Put working tree changes in your pocket for later

```bash
$ git stash
# do stuff
$ git stash pop
```

<!--
Danger! You can create merge conflicts with `git stash`.
-->

---
layout: image
image: /assets/git-stash.png
---

---

<img src="/assets/git-stash-2.png" />

---

# ~~How to make a commit~~

```bash{all|1|2|3-5|6}
$ git add .
$ git commit -m "Fixed all the things!!!"
# realize there's a random debugging print statement
$ git add .
$ git commit -m "update"
$ git push
```
---

# How to make a commit, better!

```bash{all|none}
$ git config --global user.name "Robert Boyd III"
$ git config --global user.email "git@robertboyd.dev"
$ git config --global core.editor "nvim"
# For the VSCode people:
# $ git config --global core.editor "code --wait"
$ git config --global commit.verbose "true"
```

```bash{none|1|2|3|all|none}
$ git add -p
$ git commit
# No `git push`!
```

```txt{none|all}
Restructure volumes in docker-compose.yml

The old voluming system was needlessly complicated and mounted 6 volumes when
one bigger volume works fine. This combines all the volumes into one for
simplicity.
```

<!--
1. You should be reading a diff of what you're committing.
1. Use partial add to only stage specific hunks
1. No `-m`; write a decent commit message
-->

---
layout: center
---

# Merging and the Git History

---

# Branches

Branches are pointers to commits.

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
	"branchOffset": 50
}
end
commit
commit
branch old
branch feature
checkout feature
commit
commit
checkout master
commit
commit
branch HEAD
```

<!--
Why make a branch?
1. Stable and development versions
1. Features
1. Experiments

Conventional wisdom of making lots of branches is in decline.
-->

---

# `git checkout`

Switch to a branch or restore file contents

Useful shorthands:

```bash
git checkout -b <new-branch>
```

Consider using `git switch` and `git restore` as alternatives:

```bash
git switch <existing-branch>
git switch -c <new-branch>
git switch -d <commit>
git restore <file>
```

---
layout: center
---

# `git merge`

<!--
How many people feel comfortable with `git merge`?
- Usually first command people learn after the absolute basics
- We like merging things in
- We don't like conflicts
-->

---

# `git merge`: fast-forward

<style>
.mermaid {
	height: 180px;
}
</style>

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
	"branchOffset": 20
}
end
commit
commit
commit
branch HEAD
branch feature
checkout feature
commit
commit
commit
```

After `git merge feature` while `master` was checked out:

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
	"branchOffset": 50
}
end
commit
commit
commit
branch feature
checkout feature
commit
commit
commit
checkout master
merge feature
branch HEAD
```

<!--
- Nothing can go wrong; fast-forward merges are great!
- `git pull` is a common source; it does a merge under the hood
-->

---

# `git merge`: not so fast-forward

<style>
.mermaid {
	height: 200px;
}
</style>

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
	"branchOffset": 50
}
end
commit
branch feature
checkout feature
commit
commit
commit
checkout master
commit
commit
branch HEAD
```

After `git merge feature` while `master` was checked out:

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
	"branchOffset": 50
}
end
commit
branch feature
checkout feature
commit
commit
commit
checkout master
commit
commit
merge feature
branch HEAD
```

<!--
Things actually get dicey once we have divergent histories. Git is actually
really good at merging histories, but sometimes we have to help it out.

- `git merge --no-ff`
-->

---

# Let's fix some conflicts!

It's not that hard

Sometimes we keep both:

```js {monaco}
import { foo } from "foo";
<<<<<<< HEAD
import { bar } from "bar";
=======
import { baz } from "baz";
>>>>>>> some-branch
```

Usually you want a mix:

```js {monaco}
function toFarenheit(celcius) {
<<<<<<< HEAD
	return Math.round(celcius * 9/5 + 32);
=======
	const farenheit = celcius * 9/5 + 32;
	return `${farenheit}°F`;
>>>>>>> another-branch
}
```

<!--
- Obviously conflicts can get much worse, but this is always the basic approach.

- Avoid long-running branches that diverge far from the trunk, or the merge
	conflicts will bite you.
-->

---

# The merge-everything workflow

```mermaid
gitGraph:
options
{
    "nodeSpacing": 100,
    "nodeRadius": 10,
	"branchOffset": 50
}
end
commit
branch feature
checkout feature
commit
checkout master
commit
checkout feature
merge master
commit
checkout master
commit
checkout feature
merge master
checkout master
commit
```

<!--
It's a legitimate strategy, but it creates a pretty messy history.

"There must be another way"
-->

---
layout: center
---

# `git rebase`

---

# `git rebase`

<style>
.mermaid {
	height: 200px;
}
</style>

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
	"branchOffset": 50
}
end
commit
branch feature
checkout feature
commit
commit
commit
branch HEAD
checkout master
commit
commit
```

After `git rebase master` while `feature` was checked out:

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
	"branchOffset": 50
}
end
commit
commit
commit
branch feature
checkout feature
commit
commit
commit
branch HEAD
```

---

# Rebase conflicts are the worst

```js {monaco}
function toFarenheit(celcius) {
<<<<<<< HEAD
	return Math.round(celcius * 9/5 + 32);
=======
	const farenheit = celcius * 9/5 + 32;
	return farenheit;
>>>>>>> 635fd38 (Extract return value to variable)
}
```

<div v-click>

Then we get this:

```js {monaco}
function toFarenheit(celcius) {
<<<<<<< HEAD
	const farenheit = Math.round(celcius * 9/5 + 32);
	return farenheit;
=======
	const farenheit = celcius * 9/5 + 32;
	return `${farenheit}°F`;
>>>>>>> 774e788 (Convert return value to string)
}
```
</div>

---

# `git rebase -i`

```gitrebase{monaco}
pick 635fd38 Extract return value to variable
pick 4c66f0f Fix typo
pick c01d5b6 Update npm deps
pick 635fd38 Fix bug with calculations
pick 774e788 Convert return value to string

# Rebase 5f103b9..774e788 onto 5f103b9 (2 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
```

---
layout: center
---

# When do we use `git rebase` vs `git merge`?

It's complicated

<!--
Two philosophies of Git
-->

---
layout: two-cols
---

# Accurate History

1. Reflects how the project _actually_ developed
1. No issues with history rewriting
1. Work is largely in isolation from other changes

::right::

# Clean History

1. Provides a linear, readable history
1. No useless merge commits or branch spaghetti
1. Conflicts can be caught early on

<!--
### A happy compromise?

1. Use rebase to fix your mistakes and clean up local work
1. Use merge for large changesets that may take time to develop
-->

---

# `git reset --hard`

Undo a commit by rewriting history

<style>
.mermaid {
	height: 200px;
}
</style>
```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
    "branchOffset": 30
}
end
commit
commit
commit
commit
commit
branch old
commit
branch HEAD
```

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
    "branchOffset": 30
}
end
commit
commit
commit
commit
commit
branch old
branch HEAD
```

---

# `git revert`

Undo a commit with another commit

<style>
.mermaid {
	height: 200px;
}
</style>
```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
    "branchOffset": 30
}
end
commit
commit
commit
commit
commit
branch old
commit
branch HEAD
```

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
    "branchOffset": 30
}
end
commit
commit
commit
commit
commit
branch old
commit
commit
branch HEAD
```

---

# `git cherry-pick`

Selectively rebase commits onto your current branch.

<style>
.mermaid {
	height: 210px;
}
</style>

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
    "branchOffset": 30
}
end
commit
commit
commit
branch release
branch hotfix
checkout hotfix
commit
commit
checkout master
commit
branch HEAD
```

After `git cherry-pick 93aa31d`

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
    "branchOffset": 30
}
end
commit
commit
commit
branch release
branch hotfix
checkout hotfix
commit
commit
checkout master
commit
commit
branch HEAD
```

---

# `git commit --amend`

Rewrite the last commit with less ceremony than rebase

<style>
.mermaid {
	height: 150px;
}
</style>

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
    "branchOffset": 30
}
end
commit
commit
commit
commit
branch HEAD
```

After `git add <files>` and `git commit --amend`:

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
    "branchOffset": 30
}
end
commit
commit
commit
commit "d4j8d2r"
branch HEAD
```

---
layout: center
---

# Other Stuff

---

# `git bisect`

Binary search your way to bugs!

1. `git bisect start`
1. `git bisect bad`
1. `git bisect good <old-commit-hash>`
1. Try commits and mark them good or bad
1. Git will find the first buggy commit

```mermaid
gitGraph:
options
{
    "nodeSpacing": 120,
    "nodeRadius": 10,
    "branchOffset": 30
}
end
commit
branch working
commit
commit
commit
commit
commit
branch HEAD
```

---

# `git reflog`

Where you go when you *really* screwed up...

<img src="/assets/git-reflog.png" />

<!--
The reflog contains a history of your actions, rather than the history of the
repo. It's your best chance (other than editor backups) to recover from a bad
`git reset --hard` or rebase.
-->

---

# `git blame`

Figure out who's responsible for _that_ line of code

<img src="/assets/git-blame.png" />

---

# `--first-parent`

Why I don't like squash merges

<img src="/assets/first-parent.png" />

---
layout: center
---

# `git rerere`

**Re**use **re**corded **re**solutions of merge conflicts

```
$ git config --global rerere.enabled true
```

---
layout: image
image: /assets/git-rerere.png
---

---
layout: center
---

# <a href="https://github.com/rob-3/dotfiles">https://github.com/rob-3/dotfiles</a>
