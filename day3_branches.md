# day 3 -- branches and working with others

## in the following exercice, we can observe the difference between the fast-forward and normal merge

### fast-forward merge a simple type of merge, where a new branch is created, but the original branch it stemmed from has no additional commits

![fast-forward merge](https://www.bogotobogo.com/cplusplus/images/Git/Fast_Forward_Merge/FastForwardMerge.png)

```powershell
# make a new directory and initialize the repository
mkdir exercice_fastforward
cd .\exercice_fastforward\
git init

# create a new file and add something in
vim .\myfile.txt

# stage and commit the changes
git add .\myfile.txt
git commit -m "flllirst commit"

# fix the commit message
git commit -amend -m "first commit"

# now create a new branch, switch to this branch
# and do some modification to the file
git branch new_branch
git switch new_branch
vim .\myfile.txt # do some modifications, i.e. on the 1st line
git commit -am "commit 1 new_branch"
vim .\myfile.txt # do more modifications
git commit -am "commit 2 new_branch"
vim .\myfile.txt # do more modifications
git commit -am "commit 3 new_branch"

# before merging, we need to switch to the branch
# that will receive the modifications

git switch main # switch to the main branch
git merge new_branch # fast-forward merge proposed
# it's like the HEAD from the main branch moved to 
# the latest commit

# check the log
git adog

# * d6f3cbd (HEAD -> main, new_branch) commit 3 new_branch
# * fdcf797 commit 2 new_branch
# * 715546b commit 1 new_branch
# * ffbb5aa first commit
```

### in normal commits, merge occur where different commits are found in the branches

![normal merge](https://wac-cdn.atlassian.com/dam/jcr:c6db91c1-1343-4d45-8c93-bdba910b9506/02%20Branch-1%20kopiera.png?cdnVersion=617)

```powershell
# make a new directory and initialize the repository
mkdir exercice_merge
cd .\exercice_merge\
git init

# create a new file and add something in
vim .\myfile.txt

# commit the changes
git add .\myfile.txt
git commit -m "first commit"

# now create a new branch, switch to this branch
# and do some modification to the file
git branch new_branch
git switch new_branch
vim .\myfile.txt # do some modifications, i.e. on the 1st line
git commit -am "commit 1 new_branch"
vim .\myfile.txt # do more modifications
git commit -am "commit 2 new_branch"

# go back to the main branch and do other/different modifications
git switch main

# we can list the branches at anytime
git branch
# * main
#   new_branch

vim .\myfile.txt # do some different modifications, i.e. on the last line
git commit -am "commit 1 main"
vim .\myfile.txt # do some modifications, add a new line
git commit -am "commit 2 main"

# check the history
git adog

# * c673ef3 (HEAD -> main) commit 2 main
# * 1dd691d commit 1 main
# | * 96b8db9 (new_branch) commit 2 new_branch
# | * f4471b7 commit 1 new_branch
# |/
# * ae84302 first commit


# time for the merge...
git merge new_branch 
# if no conflict, git will AUTO merge and create a commmit

# but in case of conflucts, we get the following message
# Auto-merging myfile.txt
# CONFLICT (content): Merge conflict in myfile.txt
# Automatic merge failed; fix conflicts and then commit the result.

# looking at the content of the file
git diff
# diff --cc myfile.txt
# index 52aa072,f8cc283..0000000
# --- a/myfile.txt
# +++ b/myfile.txt
# @@@ -1,4 -1,3 +1,10 @@@
# ++<<<<<<< HEAD
#  +one
#  +two
#  +drei
#  +new line from main branch
# ++=======
# + un
# + deux
# + three
# ++>>>>>>> new_branch

# fix the conflict manually in any text editor of VSCode,
# save the file (it's finally a new file...) and commit
git commit -am "fixed conflict"

# check the final log
git adog
# *   a9230bf (HEAD -> main) fixed conflict
# |\
# | * 96b8db9 (new_branch) commit 2 new_branch
# | * f4471b7 commit 1 new_branch
# * | c673ef3 commit 2 main
# * | 1dd691d commit 1 main
# |/
# * ae84302 first commit
```

### git diff displays the differences between the original file and unstaged changes, or any commit/branch

[`git diff`](https://git-scm.com/docs/git-diff
) uses the following syntax:

1. the sources of the diff, notice how it's actually the same file, just versions a and b
2. internal Git metadata or hash information
3. assigns symbols to each diff input source, here `---` refers to version A
4. and `+++` refers to version B
5. changes as `@@ -start_line,num +start_line, num@@`
6. and finally the diff chuncks acc. legend
