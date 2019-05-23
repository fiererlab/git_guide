# Hannah's Ultimate Guide to All Useful Things Git

*Guide created by Hannah Holland-Moritz*

*Updated: May 9, 2019*

## Basic git workflow

(Assuming you've already created the repository)

1. __pull__ down remote repository (usually github), in case any changes have been made.
2. Make changes to a document
	a. Use ``git status`` to determine which files have been changed, which are commited, and which need to be added. [*optional*]
3. __add__ those changes (this puts files on standby)
4. __commit__ changes (this records the changes in gits 'memory')
5. __push__ those changes to the remote repository so that everything is in sync. 

#### Working Example:

```bash
git pull # pull changes from remote repository (if it's set up)

# Writing a small file as an example
echo "Hello World" > hello.txt

git status # shows which files have been added and which are untracked

git add hello.txt # alternatively, "git add ." will add all files not already in the .gitignore file

git commit -m "message about changes made" # commits the changes. 

# WARNING: Don't skip the -m, otherwise it will open a command-line text editor and force you to write a message anyway

git push # push changes to remote repository (if it's set up)

```



## Setting up a repository
### Creating repository on your computer and syncing it to github

```bash
cd mydirectory # go into the directory first
git init # initialize directory

# Use your favorite text editor to add files that you don't want tracked to git ignore. 
# common examples include system files (prefaced with a "."), 
# files that are too large to be pushed to github, 
# files you don't want to share publicly, etc. 
nano .gitignore


git add . # add everything in the directory that is not in .gitignore

git commit # commit changes locally

```


* Go to github.
* Log in
* Click the new repository button. Don't create a README file or a .gitignore file.
* Click the “Create repository” button.

```bash
git remote add origin https://github.com/username/new_repo # connect your repo to the one on github. 

git push -u origin master # set the remote as upstream to your local directory (i.e. source of information and reference directory that changes will be pushed to.)

```

## Branches
### What are branches for?
Branches allow you to make changes to your folder without changing the "master" branch. (i.e. the original copy, or the one that you want to keep.)

### Determining what branch you're currently on

```bash
git branch
```

### Creating a new branch

```bash
# 3 ways to do this; branch name "development"

# 1 - from scratch
git branch development

# 2 - based on historic point
# Assuming everything is up to date.
# make changes to files and commit them

git log # displays commits and their identifiers

git branch development <my commit identifier> # create a branch starting at a particular historic commit

# 3 - create a branch and change to it in one command
git checkout -b development

```
### Changing between branches

```bash
git branch # determine what branch you're on and which branches are available to swich to

git checkout development # (switch from your current branch to the development branch)
```
### Pushing a branch to a remote repository (i.e. github)
Branches will not automatically be pushed to a remote repository when you use ```git push```. To do this to a branch called "development" see below:

```bash
git push -u origin development
```
### Merging a branch back into the main line safely

One good strategy to prevent any mistakes or problems that might occur when merging a branch into the main line, is to first merge the mainline into the branch and *then* merge that branch back into the main line. 

```bash
# assuming you have a branch called "development"

git checkout development # change to development branch from wherever you are right now

git merge master # merges master branch into the current branch (i.e. development)

# now resolve any merge conflicts

git checkout master # change from the development branch to the master branch

git merge development # merge branch back into development

```
### Viewing branches on remote repositories

```bash
git branch -a
```
If your branch is not listed after `git branch -a` but you're pretty sure it is there you can do the following:

```bash
# downloads changes from remote branch but doesn't change your local branch
git fetch
```
### Pulling down a branch from a remote repository
Branches won't automatically be pulled from remote repositories, to work on a branch that is listed in `git branch -a` but not local, you can create a local copy.

```bash
git checkout -b development origin/development
```

### Fancy merge tricks

### Deleting branches

```bash
git checkout master # first switch away from development branch and onto the master branch
git branch -d development # deletes development branch
```

### Deleting branches safely after merge

## Working with other people (or organizations)
### Working on someone else's project
#### Set up someone's repo as an upstream repository
```bash
git remote add upstream git://github.com/ORIGINAL-DEV-USERNAME/REPO-YOU-FORKED-FROM.git
git fetch upstream
```
#### Updating your fork from the original repo to keep up with changes
```bash
git fetch upstream # stage the changes from upstream
git pull upstream master # make them permanant; pull from upstream/master
```
#### Testing someone's pull request

```bash
# fetch the reference of the pull request based on the # ID and create a new branch in the process
git fetch origin pull/ID/head:BRANCHNAME

# if the pull request is on your upstream rather than your origin, use:
git fetch upstream pull/ID/head:BRANCHNAME
```

### Managing a project

TBA

#### Seeing the changes that someone made in order to decide if you want to accept them ####

## Fixing mistakes ##
#### Fix a commit message ####

Only works after a commit but before a push.

```bash
git commit --amend -m "fixed message goes here"
```

#### Go back to an earlier commit ####
```bash
# Figure out the commit id that you want to undo
git log 

# output from git log will be a list of commits and their comments.
# the commit id is a long number/digit combination that looks something
# like this: 027ad6ee1e540716b421f58cc7efe60fdd9267f1

# revert changes to that commit id
git revert <commit_id>
```

## Nifty tricks
#### Setting up ssh so you don't have to write your password all the time
more info here: https://kbroman.org/github_tutorial/pages/first_time.html

#### Setting up usernames and emails in git
TBA

#### Remembering what changes you made before creating a committ ####

```bash
# Before you've used "git add"

git diff myfile.txt

# After you've used "git add"
git diff --cached myfile.txt

```

#### Setting up ssh key so that remote repositories don't prompt for a username ####

1. Check for existing keys (https://help.github.com/en/articles/checking-for-existing-ssh-keys)[https://help.github.com/en/articles/checking-for-existing-ssh-keys]

```bash

ls -al ~/.ssh
# shows the files in your .ssh directory if they exist

```
If you have a key it is likely to be in a file named something like this:

```bash
id_dsa.pub
id_ecdsa.pub
id_ed25519.pub
id_rsa.pub
```
If you don't have these files, you need to generate a new key (go to step 2), otherwise you'll need skip to adding your key to the ssh-agent, if it hasn't already been done (step 3) \[doing this step twice with the same password won't hurt] and then add that key to github (step 4).

2. To generate an ssh key type this into your terminal:

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
# -b stands for number of bits
# -t is the type of key
# -C is a comment or label
```
Then give it a place to save the key.

```bash
> Enter a file in which to save the key (/home/you/.ssh/id_rsa): [Press enter]

## WARNING: do not press enter if you already have a key named "id_rsa" and you are trying to write a new one. Instead, replace "id_rsa" with your own new name. 

```

Follow the instructions on screen and then type in your passphrase when prompted. 

3. Adding your new key to the ssh-agent:

__OR__ if you have generated a new key and called it something other than *id_rsa*, replace id_rsa with that name in the code below.

```bash
# start the ssh-agent running in the background
eval "$(ssh-agent -s)"

# add your key
ssh-add ~/.ssh/id_rsa

# Now follow instructions to enter passphrase
 
```

4. Copy the contents of your public key (id_rsa.pub, in our example) to the clipboard. You can do this by concatentating it to the screen and then copying it.

```bash
cat ~/.ssh/id_rsa.pub

#[copy results to clipboard]
```

On your github page, under your avatar dropdown menu go to Settings > SSH and GPG keys > New SSH key or Add SSH key

Copy and paste the key into the space provided, and name it something helpful to remember. 


Now when you are [setting up a repository](#setting-up-a-repository) you can use the ssh rather than the https key.

## In case of Emergency, read this
#### I need to undo my commit! (and I never want to get the changes back) ####
This scenario works if you've commited locally and not yet pushed those changes to the remote repository.

```bash
# Figure out the commit id that you want to undo
git log 

# output from git log will be a list of commits and their comments.
# the commit id is a long number/digit combination that looks something
# like this: 027ad6ee1e540716b421f58cc7efe60fdd9267f1

# reset changes to that commit id
git reset --hard <commit_id>
```

#### I need to undo my ```git init```!
To undo a git init, delete the .git folder. (Only do this if you're sure you want to delete all the history and everything git-related!!)

```bash
# In the directory where you typed "git init"
rm -rf .git
```

#### I accidentally added and committed a file that was too large for github to handle! ####

If this happens you'll see this error:

```bash
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
remote: error: GH001: Large files detected. You may want to try Git Large File Storage - https://git-lfs.github.com.
remote: error: Trace: c36787f5781bee6d8e4e0b6192d15936
remote: error: See http://git.io/iEPt8g for more information.
remote: error: File largefile.txt is 126.28 MB; this exceeds GitHub's file size limit of 100.00 MB
To https://github.com/gitusername/myrepository.git
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to 'https://gitusername@github.com/githusername/myrepository.git'
```

__Suggestion:__ If you are not used to working with a commandline editor, I recommend changing your default git editor to ```nano``` before you start this process (since it's more intuitive than the default ```vim```). 

To change your editor: 

```bash
git config --global core.editor nano # changes git editor to nano for all git-related tasks

```

Fixing your commit history:

Git rebase is your new best friend. Git rebase merges several commits into one (among other things). This will allow you to delete the file, commit that change and then merge the addition and deletion of the file into one commit step so that git doesn't try to push the large file to github and get stuck.

Steps: 
1. Delete the large file and commit the change.
2. __squash__ the two commits into one large commit, using rebase.
3. __commit__ those changes to the history
4. __pull__ from the remote repository so you don't create conflicts with the commit history.
5. __push__ the new commit history to github.


```bash
## remove file and commit changes
rm largefile.txt # remove the offending file

git status

git rm largefile.txt

git commit -m "removing the large file that I accidentally added"

## view the commits you've made

git log # should show a history of commits you've made, count back to the one where you added largefile.txt

# Type "q" to get out of git log

## Squash or remove the commits into one large commit

git rebase -i HEAD~10 # open up and interactive git rebase command going back 10 commands

# this opens up a text editor showing your options in comments; edit the "pick" on the  commands that you want to change.
# I like to squash the commit where I added the file and the commit where I removed it into the command below them.
# When you are finished editing the file. Save the output (ctrl + O, in nano) and exit (ctrl + X, in nano).
# If promted, write a commit message telling what you did to change the history.

## Pull from the remote repository

git pull # (if promted write a commit message)

## Push your changes to the commit history
git push

```


#### My situation isn't covered here! ####

Checkout this site: https://github.blog/2015-06-08-how-to-undo-almost-anything-with-git/