# day 5 -- GitHub and git in practice

## stash

Temporarily shelves (or stashes) changes you've made to your working copy so you can work on something else, and then come back and re-apply them later on. Stashing is useful when you find yourself needing to quickly switch branches to work on something else, but are in the middle of changing a file and not reading for commit. So you don't bring the latest changes on the files comming from the initial branch from the new branch while switching back to the initial branch. In case of conflicts, the switch is even impossible. So `git stash` changes to preserve them without a commit

![stash](https://i.ytimg.com/vi/wpHEjqIjJGQ/maxresdefault.jpg)

Note that by default Git won't stash changes made to untracked or ignored files.

```powershell
# prepare the demo
mkdir day5_demo
git init
vim myfile.txt
git add .\myfile.txt
git commit -m "initial commit on main"
git adog
# * c28f668 (HEAD -> main) initial commit on main

# create a new branch, swith to it and create another file
git checkout -b other_branch
git branch
#   main
# * other_branch

vim anotherfile.txt
git status
# On branch other_branch
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#         anotherfile.txt

git switch main
git status
# On branch main
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#         anotherfile.txt

# working changes not conflicting, so they follow with the switch
# but maybe we want those changes to be stashed away before switching

git switch other_branch
git add .\anotherfile.txt
git commit -m "other commit"
# now apply changes to myfile.txt
vim .\myfile.txt

git switch main
ls
# only 'myfile.txt' is listed

cat .\myfile.txt
# modified in other_branch 

# we "lost" the content of the original file in the main branch!

git switch other_branch
git restore .\myfile.txt
cat .\myfile.txt
# committed on main branch

# let's add a line to the original file
vim .\myfile.txt
cat .\myfile.txt
# committed on main branch
# WORKING CHANGES ON OTHER BRANCH

git stash
# Saved working directory and index state WIP on other_branch: b157e42 other commit

git switch main
cat .\myfile.txt
# committed on main branch

# let's make some changes in the main branch
vim .\myfile.txt
cat .\myfile.txt
# committed on main branch
# ---another commit on main

git commit -am "update myfile.txt"
git switch other_branch
cat .\myfile.txt
# committed on main branch

git stash list
# stash@{0}: WIP on other_branch: b157e42 other commit

git stash pop
# On branch other_branch
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git restore <file>..." to discard changes in working directory)
#         modified:   myfile.txt

git adog
# * 39191ee (main) update myfile.txt
# | * b157e42 (HEAD -> other_branch) other commit
# |/
# * c28f668 initial commit on main
```

### stash pop vs. apply

`git stash pop` throws away the (topmost, by default) stash after applying it, whereas `git stash apply` **leaves it in the stash list** for possible later reuse (or you can then `git stash drop` it). This happens unless there are conflicts after git stash pop, in which case it will not remove the stash, leaving it to behave exactly like git stash apply.

Another way to look at it: `git stash pop` is `git stash apply` && `git stash drop`.
