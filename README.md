# GitProTips

# New to Git?

## Great beginner git videos:

- https://www.youtube.com/user/mahler711

- Specifically: 
  
  - https://www.youtube.com/watch?v=uR6G2v_WsRA
  - https://www.youtube.com/watch?v=FyAAIHHClqI
  - https://www.youtube.com/watch?v=Gg4bLk8cGNo

Visual Git Reference:

- http://marklodato.github.io/visual-git-guide/index-en.html

- Basic definitions
  
  - working tree - your local file system
  - staging area (index) - changes that are going to go into the next commit
  - history - stored in .git dir. Every commit ever made.
  - commit - a snapshot of the file system at a point in time. Like a saved game.
  - head - where you are, right now. Head points to a commit on a branch (or master). It's a point in your git history.
  - branch - a pointer to a specific commit in git history. When you are on the branch, you append to it when you commit.

# Authentication

## Change HTTPS -> SSH

```
git remote -v  #check on current remote
git remote set-url origin git@github.com:USERNAME/OTHERREPOSITORY.git
git remote -v #it should be fixed now

git push # if auth works it will say 'everything up to date'
```

## Change SSH -> HTTPS

same as above but use HTTPS
`git remote set-url origin https://github.com/dlardo/kudu.git`
More: https://help.github.com/articles/changing-a-remote-s-url/

## Push existing files into a new repo
* Create a new, empty repo on github.com
  * https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository
* copy & edit the NEWREPO= variable below. Paste into your bash shell
```
export NEWREPO="perforce"
echo "# ${NEWREPO}" > README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:dlardo/${NEWREPO}.git
git push -uf origin main
unset NEWREPO
git status
```
* edit your .gitignore, commit and push files as usual

# Remote Branches

## list remote branches and then check them out

```
git fetch origin
git branch -a
git checkout -b branch_name origin/branch_name
```

more: http://gitready.com/intermediate/2009/02/13/list-remote-branches.html

## remove stale remote branches that have been deleted

`git remote prune origin`

# Git rebase

```
git checkout master
git pull
git checkout <branch>
git rebase -i master  # -i for "interactive".  Use gitpad or fix editor
git push -f # force
```

## Rebase when you are a fork

more: https://stackoverflow.com/questions/7244321/how-do-i-update-a-github-forked-repository

## From your main branch, add the remote, call it "upstream", fetch, rebase, push -f

```
git remote add upstream https://github.com/whoever/whatever.git
git fetch upstream
git rebase upstream/master
git push -f
```

# Undo

Roll back uncommited changes on your local system:
`git checkout -- <bad filename>`

## Roll back several commits on your local system:

https://github.com/blog/2019-how-to-undo-almost-anything-with-git

## Rollback your main branch example

```
git checkout main
git checkout -b main-backup
git push --set-upstream origin main-backup
git checkout main
git reset --hard e6cad43258469a737898530bf3b1641d2abbac34
git push -f
```

# Windows

## Add +x in windows

- `git update-index --chmod=+x vpc_init_check.sh`
- commit file & push

## Enable Symlinks
- Windows Search > "developer settings" > Set Developer mode to true
- `git config --global core.symlinks true`

# .gitignore

## apply .gitignore retroactivley

- make changes to .gitignore and commit them
- clear repo: `git rm -r --cached .`
- readd everything: `git add .`
- commit: `git commit -m ".gitignore fix"`
- more: http://www.codeblocq.com/2016/01/Untrack-files-already-added-to-git-repository-based-on-gitignore/

# Rebase

## Overview

- Resolve easy/obvious conflicts. 
- Be CAREFUL as 'ours' and 'theirs' is flipped from what you might think when you rebase.
- When you rebase you are starting from the perspective of what is on github.com, and then you are adding in "somebody else" (theirs) changes on your existing (and behind main) branch.

- Select your branch: `git checkout --theirs PATH/FILE`
- Select from main (good for undoing your work): `git checkout --ours PATH/FILE`
- If you have multiple files and you want to accept the new branch (probably your work). Yes works in windows git shell. `grep -lr "<<<<<<<" . | xargs git checkout --theirs`
- If you have multiple files and you want to accept main (good for undoing your work), run: `grep -lr "<<<<<<<" . | xargs git checkout --ours`

## You branched from a branch and now it's weird.

- Can't wait? Want to build off your existing work? New branch off the branch currently in review. (Not recommended)
- Eventually the the first PR will get merged, and now you are in an odd spot.
- Your branch of a branch will look like it contains changes from both branches in it.
- The Fix: Rebase the second branch onto the updated main.
- Then force push the branch of branch.
- It will look right afterwords.

# Tagging

## How to tag a release / sha

```
$ git show
commit c49c0c6c091652d12bb5a97ad56887ed6d10f7af
Author: Doug Lardo <dlardo@example.com>
Date:   Wed Oct 18 18:48:25 2017 -0700
<diff details follow>
Find the sha for the commit you want to tag. Tag it, push it to origin.

git tag c49c0c6c091652d12bb5a97ad56887ed6d10f7af baconqa-trisaber-usw3-rcluster1
git push origin baconqa-trisaber-usw3-rcluster1
git tag
```

## show what commit a tag points to:

```
git rev-list -n 1 <tag>
9a788c7186e26a17f4c31b9c9dab9a60691d3623
```

## remove tag (local and remote>

```
git tag -d <tag>
git push --delete <tag>
```

more: https://gist.github.com/mobilemind/7883996

# Performace

## Compress Git Repo

- You probably don't want to do this unless you are in a bad spot. You will need everyone on your team to clone from scratch.
- We basically tell git that your current commit is the initial commit

Steps

- Backup everything by cloning the bloated repo to a new directory.
- In your "to be compressed" copy of the repo, get to the commit that will be your "new commit 0" on your local machine. Probably HEAD.
- Delete any local branches and tags that might contain the old history.

Then run:

```
git checkout — orphan latest_branch
git add -A
git commit -am “Initial commit message” # Committing every file as if it was brand new
git branch -D master # Deleting master branch
git branch -m master # renaming branch as master
git push -f origin master #pushes to master branch
git gc --aggressive --prune=all # remove the old files
```

Have everyone else clone a fresh copy into an empty dir. If you try to pull from main you will get a "unrelated histories" error. More info: https://www.educative.io/edpresso/the-fatal-refusing-to-merge-unrelated-histories-git-error

# How to split up a PR that got out of control

- Problem - you made a giant PR and your team hates you. Breaking it up into smaller PRs would get them to hate you less.
- So you have a giant branch, let's call that "giant_list_of_changes". This is what we are going to break up into little branches.

## Create a sub branch

- Handy commands: https://orga.cat/posts/most-useful-git-commands
- More info: https://derwolfe.net/2016/01/23/splitting-up-pull-requests/
- git simple things: http://rogerdudler.github.io/git-guide/

## checkout & update master

```
git checkout master
git pull
```

## Create a new branch

- switch to a new branch that will eventually have only a subset of your changes
- `git checkout -b just_logging`

## Checkout work from the big branch

- `git checkout -p giant_list_of_changes -- src/pick/a/file.py`

- The `-p` flag lets you use the interactive interface to show the diff between your working tree and the giant_list_of_changes branch.

- Use Y(es), N(o), A(ll) for this file, and a bunch of other more advanced options.

- FYI When you `checkout -p <branch>`, you modify your working tree, and you update your index (staging area) as well.

- Continue adding files / chucks until you are happy, then commit and push

- `git commit -m "just logging things"`
- `git push --set-upstream origin just_logging`

## Go to gh, create a PR, get that subset reviewed.

## Create another branch

- Next, we will need to create at least one more branch, as the whole idea was to chop this up into smaller bits, so you will have files left over that you haven't commited yet.

- checkout master and create a new branch, or if you are feeling pro:
  `git checkout -b "just_the_readme" master`

- Pull in the files you need, using the same command as before. Commit & push & get a PR.
  
  ```
  git checkout -p giant_list_of_changes -- src/pick/another/file.py
  git commit -m "just readme things"
  git push --set-upstream origin just_the_readme
  ```

## Did we get everything?

- Now we've made a bunch of little PRs but we aren't sure if we got everything from `giant_list_of_changes` Let's 2x check it.

- We don't want to mess with `giant_list_of_changes`, as that is our definition of what all the branches should sum up to.

- Instead let's create a new branch that contains all the commits from the little branches, and then compare that against giant_list_of_changes
  
  ```
  git checkout master
  git checkout -b double_check
  git merge just_the_readme
  git merge just_logging
  ```

- Checkout giant_list_of_changes so you can diff the two branches. You can diff from here but the + / - feels backward unless you switch
  
  ```
  git checkout giant_list_of_changes
  git diff double_check
  ```

- list of files you missed: `git diff --name-only double_check`

- Go back to master, create a new branch, add missing changes, commit, push, PR.
  
  - Repeat as needed.
  
  - You can also checkout existing branches and add more files / commits if you missed something.

# How do I merge all this back into master? Where do I put fixes?

- Since you aren't doing a straight forward merge any more, the "fast forward" merge option isn't available. What happens instead is a 3 way merge.

- Why is a a 3 way? Well if you merge branches together, and both change the same line of code, it's impossible to tell (without asking) what the correct one is without knowing what the starting point was. So, if we know what things looked like before we started, we can automatically resolve a lot of conflicts between the two branches.

- 1 - branch A

- 2 - branch B

- 3 - Starting point (common ancestor), or simply 'base'

- When there is a conflict that can't be resolved, the user must decide.

- Merge conflict Terms:
  
  - Theirs: the source of the merge. Typically the branch.
  
  - Base: common ancestor. Typically the commit in master where the branches were started from.
  
  - Yours: the destination of the merge. Typically master.
  
  - More info: http://www.drdobbs.com/tools/three-way-merging-a-look-under-the-hood/240164902?pgno=2

- When we do a 3 way merge we diff the last commit in each branch with the nearest common ancestor. The nearest common ancestor can change if you pull changes in from master into your dev branch. This is why it's good to resolve conflicts early and often so they don't build up over time.

- So what this all means is that you can put your fixes into each small branch, and merge them into master when they are ready.

- Your giant_list_of_changes will then will be missing a few changes that you put into each sub branch.

- If you did your double_check branch correctly, you know that everything was included and you can delete the giant_list_of_changes branch.

- If you want to preserve the commits (probably not worth), you can merge the giant_list_of_changes branch into master. It will do a 3 way merge (3 way diff) and determine that both master and giant_list_of_changes are basically identical and merge it all together without issue. There may be some merge conflicts if the fixes (new commits) in the small_branches modified the same lines.

- You can practice merging to master by creating a new branch off master, then merging giant_list_of_changes into that.
  
  ```
  git checkout master
  git pull
  git checkout -b practice_merge
  git merge giant_list_of_changes
  ```

- You should only see some of the fixes you made in your sub-branches that weren't in your original giant_list_of_changes branch.

- This will be 3 way merging:
  
  - 1 - last commit giant_list_of_changes
  - 2 - last commit of practice_merge
  - 3 - common ancestor - probably the point at master when giant_list_of_changes was created.

- This means that git won't be able to tell between a change on branch 1 and on branch 2 on the same line of code, so you will have to manually resolve them.

- If you diff your practice_merge branch with master, you should see only the fixes you added in the sub-branches. If everything looks right, then your master will be up to date, including all fixes.

- If you see more than that, it means you missed code when you were breaking out into sub-branches. Use the git checkout -p to a new branch, get that reviewed and merge it into master. Try your diff again, repeat as needed.

# You didn't break it

- If you create a commit, it is never lost within git. github will also keep it, however if it's not tracked on a branch, it's more difficult to find.

- Experiment: Created a new branch, never_die_test
  
  - * 062c2ab (origin/never_die_test, never_die_test) never die commit msg
  - committed, pushed to master
  - Deleted the local branch, and deleted the upstream tracked branch
  - gh showed it existing, and then gone again

- if you do a `git log 062c2ab`, you can see the commit.

- `git checkout 062c2ab`
  
  - You will be in a detached head, but your local filesystem will update, and you can see the change is back in your working tree.
  - `git checkout master` gets you out of the detached head state. It will warn about your abandoned commit as it's not associated with a branch, but you can always create a new branch with: `git branch <new-branch-name> 062c2ab`

- Also, since we pushed to gh, you can view the commit at: https://github.com/dlardo/repo/commit/062c2ab

# New Machines

## New Linux Machine:

```
mkdir ~/.ssh
chmod 700 ~/.ssh
copy in ~.ssh/id_rsa.pub and id_rsa
chmod 600 ~/.ssh/id_rsa*
```

- Test with: `ssh -T git@github.com`
  - You should get: "Hi dlardo! You've successfully authenticated, but GitHub does not provide shell access."

## View all git settings

- `git config --list --global`

# .gitconfig files

http://michaelwales.com/articles/make-gitconfig-work-for-you/

# Proxy Servers

- Set: `git config --global http.https://github.com.proxy http://192.168.0.70:8080`
- Remove: `git config --global --unset http.https://github.com.proxy`
- more: https://gist.github.com/evantoli/f8c23a37eb3558ab8765
