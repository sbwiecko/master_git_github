# day 2 -- working with a remote repository

in the following workflow, we initialize the current local repository, create the corresponding remote repository on GitHub, push the local repository on GitHub, create a file online and commit it, fetch it and checkout it in the detached HEAD mode.

## create the local repository

```powershell
# initialize the repo at the root of the current directory
git init

# check the status of the untracked files
git status

# stage all files and create the initial commit
git add .
git commit -m "initial commit"

# look at the log
git log --oneline
# 7ec5d33 (HEAD -> main) initial commit
```

## create the remote repository ~ remote branch

1. go to GitHub and create a new repository
2. push the local repository from the command line acc. instructions

```powershell
# add the remote branch (SSH)
# the 'origin' name for remote is just a convention
git remote add origin git@github.com:sbwiecko/master_git_github.git

# list the remote connections/branch
git remote -v

# if current branch is nammed master, we can rename to main
#git branch -M main

# push to remote
git push -u origin main # --set-upstream (add tracking reference)

# there may be permission errors depending on the setting up of the
# security policy of the remote repository on GitHub

git adog
# * 7ec5d33 (HEAD -> main, origin/main) initial commit
```

### rename and remove remote branch

```powershell
# we can rename the origin
git remote rename origin origin_new

# or delete it
git remote remove origin_new
```

## fetch and pull

Using `git fetch` will download changes from the GitHub remote repository, however you will not see those changes be part of the files you have in the working directory. Fetch allows you to grab additional work done on the remote master branch, without needing to merge it in your working directory files. The changes are in the **local repository**.

Using `git fetch` makes sense when you're working with others and want to see what changes they've made but aren't ready to overwrite your own files yet. Also if you are simply working by yourself, you may want to just fetch remote changes without overwriting your latest work (later we'll discover branches are a better way of doing this).

Using `git pull` makes sense when you want to directly grab changes from the remote branch and directly merge them into your current branch. This means you will literally update the files in your **working directory** to match up and merge with the remote branch.

![fetch and pull](https://miro.medium.com/max/600/1*SKR0Zz4S0M_0Rp-aPsZw0Q.png)

```powershell
# first, create a new text file with some content in the remote repository on GitHub
# commit the change and reconnect the remote to main (has been deleted for the exercice)

git remote add origin git@github.com:sbwiecko/master_git_github.git

# fetch
git fetch

# git log doesn't show the remote branch origin/master
# HEAD is still pointing to the main and no files are changed
# checkout the remote branch locally w/o overwriting the
# local content, but now seing the content created remotely
git checkout origin/main

git branch -a
# * (HEAD detached at origin/main)
#   main
#   remotes/origin/main

git adog
# * 7cda32d (HEAD, origin/main) added file remotely
# * 7ec5d33 (main) initial commit

# switch back to the main branch
git switch main

# we can merge (fast-forward in that case)
git merge origin/main

# but fetch and merge can be done at once using `git pull`
```
