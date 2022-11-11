# day 4 -- undoing changes

## checkout and switch

`git checkout` is actually a very versatile command, so versatile in fact, that developers complained it was used for too many different actions, thus new git commands were created, such as git switch. In contrast to `git switch`, the `git checkout` command can operate on three distinct entities: files, commits, and branches. For example, we could use `git checkout <branch>` instead of `git switch <branch>` to checkout a new branch. Unlike `git switch` however, recall checkout can operate on commits, meaning we can "checkout" historical commits, by specifying its hash, in a detached HEAD state.

### HEAD

HEAD points to the branch which points to the latest commit. Upon calling `git checkout` we detach the HEAD to a previous commit. This command does not undo previous work, you are simply exploring the historical commit. If you started making changes here, they won't be preserved since **HEAD is not pointing at a branch reference**. However you could create a new branch at this point in time, using the `git switch -c <new-branch-name>` command as indicated, reattaching HEAD to a branch again.

![detached HEAD](https://miro.medium.com/max/638/1*lCRzyyyCuj7Vq4nKJIXwBg.jpeg)

```powershell
# create a series of commits, for each one line is added to the TXT file
mkdir day4
cd ./day4
vim myfile.txt # add first line
git add .\myfile.txt
git commit -m "first commit" 

vim myfile.txt # add second line
git commit -am "second commit"

vim myfile.txt # add second line
git commit -am "third commit"

git adog
# * 5db4e37 (HEAD -> main) third commit
# * 9f11c92 second commit
# * 1a5d20f first commit

# checkout the state at the second commit
git checkout 9f11c92

# You are in 'detached HEAD' state. You can look around, make experimental
# changes and commit them, and you can discard any commits you make in this
# state without impacting any branches by switching back to a branch.

cat .\myfile.txt
# first commit
# second commit

# interstingly, log show HEAD points to any branch
git log
# commit 9f11c927c12a7dfd41ca15501dd0f8fe9b08ec1d (HEAD)

# go back to most recent commit in main
git checkout main 
git log

# commit 5db4e37d6fabf670b47e68719a6a37df791c979a (HEAD -> main)
```

## restore

### restore a file to the most recent commit

We can restore a file to its state at the previous most recent commit using the `git restore <filename>`. You can not undo a restore, since your most recent changes were not committed! Think of this command as an ultimate "Ctrl+Z" restoring files to their previous commit. Technically speaking `git restore` will restore the file back to the HEAD, which typically we have pointing to the most recent commit in the branch.

```powershell
# make some changes to myfile.txt, save the file
git status

# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git restore <file>..." to discard changes in working directory)
#         modified:   myfile.txt

git restore myfile.txt
```

### restore a file to any commit

This actually gives us even more flexibility in our restore procedure, we can restore a file to any commit in the log. We state the number of commits from the HEAD to go back to `git restore --source HEAD~N <filename>`. These modifications are not staged yet, so we can always undo such a restore.

```powershell
# go back to the state 2 commits prior to the current commit
git restore --source HEAD~2 myfile.txt

cat myfile.txt
# first commit

git status
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git restore <file>..." to discard changes in working directory)
#         modified:   myfile.txt
```

### unstage files

Finally, git restore also allows us to unstage files that we had already added to the staging area with `git restore --staged <filename>`.

```powershell
# let's stage the previous state of myfile.txt
git add myfile.txt

git status
# Changes to be committed:
#   (use "git restore --staged <file>..." to unstage)
#         modified:   myfile.txt

git restore --staged .\myfile.txt
git adog
# * 5db4e37 (HEAD -> main) third commit
# * 9f11c92 second commit
# * 1a5d20f first commit

# finally, restore myfile.txt to the state of the 3rd commit
git restore myfile.txt
cat .\myfile.txt

# first commit
# second commit
# third commit
```

## reset

`git reset` allows us to remove commits and "reset" the branch. There are two main types of git reset calls. We can visualize a git reset moving back to a previous commit, but not undoing file changes (unless it is --hard).

1. `git reset #######`, removes commits in front of the specific hash called, files unchanged. It's like moving the last files staged and committed back to the working directory. This means when you do git reset you won't notice a change in the files themselves, you just reset the commits. This is useful if you accidentally committed to the wrong branch.
2. `git reset ####### --hard`, removes commits **and** the changes in the files. In the case where you just want to **undo everything**, including changes and have the branch files look like they did at a previous commit, you add the flag --hard.

_Technically you can try to recover a commit before Git does its garbage collection, however you should operate under the assumption that a --hard reset is not recoverable._

![git reset](https://i.stack.imgur.com/Tiv4H.png)

```powershell
# let's use the myfile.txt file prepared previously, with 3 lines -- 3 commits
git log --oneline

# 5db4e37 (HEAD -> main) third commit
# 9f11c92 second commit
# 1a5d20f first commit

# reset the branch to the state of the 2nd commit
git reset 9f11c92
git adog

# * 9f11c92 (HEAD -> main) second commit
# * 1a5d20f first commit

# HEAD moved back to the 2nd commit, 3rd commit was removed,
# though, the file hasn't changed yet and is in the staging area
git status

# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git restore <file>..." to discard changes in working directory)
#         modified:   myfile.txt

git commit -am "update commit"
git log --oneline

# 6b254c7 (HEAD -> main) update commit
# 9f11c92 second commit
# 1a5d20f first commit

# we are back at the same state as before but third commit is different

# let's remove the third line in the file
git reset 9f11c92 --hard
cat .\myfile.txt

# first commit
# second commit

git adog

# * 9f11c92 (HEAD -> main) second commit
# * 1a5d20f first commit
```

## revert

The `git revert` command will create a new commit that undoes work from previous commits, but keeps those commits in the branch. Indeed, a `git reset` goes back and removes the commits, and we lose **shared history**, e.g., when a branch was created from one of those deleted commits.

A `git revert` creates a new commit that matches the historical state of a previous commit. It doesn't change the project history, which makes it a "safe" operation for commits that have already been published to a shared repository. It allows to revisit the whole history.

![git revert](http://blog.nakulrajput.com/wp-content/uploads/2018/10/Git-Reverting-Resetting.jpg)

The `git revert` command is a forward-moving undo operation that offers a safe method of undoing changes. Instead of deleting or orphaning commits in the commit history, a revert will create a new commit that inverses the changes specified, and therefore is a safer alternative to git reset in regards to losing work.

```powershell
# add some 'buggy code' in the current myfile.txt file
vim myfile.txt 
git commit -am "buggy commit"
git adog

# * f5d1859 (HEAD -> main) buggy commit
# * 9f11c92 second commit
# * 1a5d20f first commit

# now we ask which commit to revert, or make a copy
git revert f5d1859

# create a merge, asking for a message to add to the commit
# now the buggy code was removed
cat .\myfile.txt

# first commit
# second commit

# but we have access to the whole history
git adog

# * 11e98b7 (HEAD -> main) Revert "buggy commit"
# * f5d1859 buggy commit
# * 9f11c92 second commit
# * 1a5d20f first commit
```

## hands-on exercice

```powershell
# Clone the repository and look at the log and confirm the branch name
git clone https://github.com/Pierian-Data/day-four-undo-exercise-file
cd .\day-four-undo-exercise-file\
git adog

# * 1d9c172 (HEAD -> main, origin/main, origin/HEAD) fourth commit
# * f6f4273 third commit
# * 461e423 second commit
# * 5f2f515 first commit

# Without permanently changing the content of the file at any commit,
# view what the file looked like after the "first commit"
git checkout 5f2f515 # You are in 'detached HEAD' state.
cat .\myfile.txt

# After viewing the file, move the HEAD back to the current main branch
git checkout main

# Go back to commit with message "second commit" and create a new branch
# called new_branch starting from that point (do not delete commits that
# came after this one)
git checkout 461e423

# Stay on the main branch pretend like you "forgot" to switch to your new branch.
# git switch -c new_branch would create and switch to new_branch
git branch new_branch
git adog

# * 1d9c172 (origin/main, origin/HEAD, main) fourth commit
# * f6f4273 third commit
# * 461e423 (HEAD, new_branch) second commit
# * 5f2f515 first commit

# While on the main branch at the second commit, delete everything
# in the file using the text editor and your delete key
vim .\myfile.txt # delete everything and save

# Now using Git commands only (not just Ctrl+Z) restore the file so
# it matches what it looked like at the second commit (right before
# you deleted everything)
git restore .\myfile.txt

# While still on the main branch at the second commit, add a new
# line that says "new branch text".
vim .\myfile.txt

# Add and Commit this change to the file.
# Realize that you meant to actually do this change on your
# new_branch, figure out how to undo the commit on the main branch
# without losing your work, then switch branches and do the add/commit.
git commit -am "new branch text"
git adog

# * ce2c8d3 (HEAD) new branch text
# | * 1d9c172 (origin/main, origin/HEAD, main) fourth commit
# | * f6f4273 third commit
# |/
# * 461e423 (new_branch) second commit
# * 5f2f515 first commit

git reset 461e423
# Unstaged changes after reset:
# M       myfile.txt

git switch new_branch
# Switched to branch 'new_branch'
# M       myfile.txt

git commit -am "fixed new branch text"
git adog

# * bff07b5 (HEAD -> new_branch) new branch text
# | * 1d9c172 (origin/main, origin/HEAD, main) fourth commit
# | * f6f4273 third commit
# |/
# * 461e423 second commit
# * 5f2f515 first commit
```