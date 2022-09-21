# GitProTips

# Change HTTPS -> SSH
git remote -v  #check on current remote
git remote set-url origin git@gh.riotgames.com:USERNAME/OTHERREPOSITORY.git
git remote -v #it should be fixed now

git push # if auth works it will say 'everything up to date'

# Change SSH -> HTTPS
same as above but use HTTPS
git remote set-url origin https://gh.riotgames.com/dlardo/kudu.git
More:
https://help.github.com/articles/changing-a-remote-s-url/

# list remote branches and then check them out
git fetch origin
git branch -a
git checkout -b branch_name origin/branch_name
more: http://gitready.com/intermediate/2009/02/13/list-remote-branches.html

# remove stale remote branches that have been deleted
git remote prune origin

# Git rebase
git checkout master
git pull
git checkout <branch>
git rebase -i master  # -i for "interactive".  Use gitpad or fix editor
git push -f # force 

# Rebase when you are a fork
more: https://stackoverflow.com/questions/7244321/how-do-i-update-a-github-forked-repository
# from your master, add the remote, call it "upstream", fetch, rebase, push -f
git remote add upstream https://github.com/whoever/whatever.git
git fetch upstream
git rebase upstream/master
git push -f

# undo
Roll back uncommited changes on your local system:
git checkout -- <bad filename>

# Roll back several commits on your local system:
https://github.com/blog/2019-how-to-undo-almost-anything-with-git

# Rollback your main branch example
git checkout main
git checkout -b main-backup
git push --set-upstream origin main-backup
git checkout main
git reset --hard e6cad43258469a737898530bf3b1641d2abbac34
git push -f

# Add +x in windows
git update-index --chmod=+x vpc_init_check.sh
commit file & push

# apply .gitignore retroactivley
more: http://www.codeblocq.com/2016/01/Untrack-files-already-added-to-git-repository-based-on-gitignore/
make changes to .gitignore and commit them
clear repo: git rm -r --cached .
readd everything: git add .
commit: git commit -m ".gitignore fix"

# Rebase - Resolve easy/obvious conflicts. Be CAREFUL as 'ours' and 'theirs' is flipped from what you might think when you rebase. Ours seems to be gh.riotgames.com

Maybe because when you rebase you are starting from the perspective of what is on gh.riotgames.com, (that point in time before any changes were made) and then you are adding in "somebody else" (theirs) changes.

Select your branch:
git checkout --theirs PATH/FILE
Select from master (good for undoing your work):
git checkout --ours PATH/FILE

If you have multiple files and you want to accept the new branch (probably your work). Yes works in windows git shell.
grep -lr "<<<<<<<" . | xargs git checkout --theirs

If you have multiple files and you want to accept master (good for undoing your work), run:
grep -lr "<<<<<<<" . | xargs git checkout --ours

# Waiting on a PR, so you branch off the branch?
You probably got sick of waiting for the PR to get accepted, but you didn't want to go back to master.
Then the first PR got merged, and now you are in an odd spot.
Your branch of a branch will look like it contains changes from both branches in it.
You want to rebase the second branch onto the updated master.
Then force push.
It will look right afterwords.
Zvi can explain with a whiteboard. It has something to do with the common ancestor in a 3 way merge.

# Tag a release / sha
$ git show
commit c49c0c6c091652d12bb5a97ad56887ed6d10f7af
Author: Doug Lardo <dlardo@riotgames.com>
Date:   Wed Oct 18 18:48:25 2017 -0700
<diff details follow>
Find the sha for the commit you want to tag. Tag it, push it to origin.

git tag c49c0c6c091652d12bb5a97ad56887ed6d10f7af baconqa-trisaber-usw3-rcluster1
git push origin baconqa-trisaber-usw3-rcluster1
git tag

# show what commit a tag points to:
git rev-list -n 1 <tag>
9a788c7186e26a17f4c31b9c9dab9a60691d3623

# remove tag (local and remote>
git tag -d <tag>
git push --delete <tag>

# more: https://gist.github.com/mobilemind/7883996

## Compress Git Repo
# We basically tell git that your current commit is the initial commit. 
# Backup everything. Clone the bloated repo to a new directory.
# In your "to be compressed" copy of the repo, get to the commit that will be your "new commit 0" on your local machine. 
# Delete any local branches and tags that might contain the old history.
Then run:
git checkout — orphan latest_branch
git add -A
git commit -am “Initial commit message” # Committing every file as if it was brand new
git branch -D master # Deleting master branch
git branch -m master # renaming branch as master
git push -f origin master #pushes to master branch
git gc --aggressive --prune=all # remove the old files
# Have everyone else clone a fresh copy into an empty dir. If you try to pull from master you will get a "unrelated histories" error. More info: https://www.educative.io/edpresso/the-fatal-refusing-to-merge-unrelated-histories-git-error

## How to split up a PR that got out of control:
Read "C:\Users\dlardo\Nextcloud\Documents\git pro tips\How to split up PRs.txt"

## New Windows PC:
See your Win10 Fresh Install instructions. You have a bat file in NextCloud\SSH Helpers\ that sets up Git properly

## New Linux Machine:
mkdir ~/.ssh
chmod 700 ~/.ssh
copy in ~.ssh/id_rsa.pub and id_rsa
chmod 600 ~/.ssh/id_rsa*

Test with: ssh -T git@github.com or git@gh.riotgames.com
You should get: "Hi dlardo! You've successfully authenticated, but GitHub does not provide shell access."

# View all git settings
λ git config --list --global

# This might be worth checking out. a .gitconfig file
http://michaelwales.com/articles/make-gitconfig-work-for-you/

# Git uses a proxy
git config --global http.https://gh.riotgames.com.proxy http://192.168.0.70:8080

Remove:
git config --global --unset http.https://domain.com.proxy

more: https://gist.github.com/evantoli/f8c23a37eb3558ab8765

# Great git videos from a Network engineer:
https://www.youtube.com/user/mahler711

Specifically: 
https://www.youtube.com/watch?v=uR6G2v_WsRA

https://www.youtube.com/watch?v=FyAAIHHClqI

https://www.youtube.com/watch?v=Gg4bLk8cGNo

Visual Git Reference:
http://marklodato.github.io/visual-git-guide/index-en.html
