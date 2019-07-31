




<!---

# Intro

#>  # GIT_SSH_COMMAND="ssh -v" git push

This document was tested on **Ubuntu 18.04** with **git version 2.17.1**.

# Installation

```
# install git
sudo apt install git

# setup user.
git config --global user.name "John Doe"
git config --global user.email "john.doe@gmail.com"

# to list all config - local (per repo), global (per user), system (per machine)
git config --list --show-origin
```

# Repo Setup for Development

*Coming Soon*

# Frequently Used Commands




# shows all the tags with what you annnotated with.
git tag -n















































  git config --global alias.logp 'log --pretty=format:"%C(yellow)%h %Cblue%>(12)%ad %Cgreen%<(8)%aN%Cred%d %Creset%s" --date=relative --graph'
#>  git logp --all







= Tips =

== Logs ==

* view all repo logs:

 git log --pretty=format:"%C(yellow)%h %Cblue%>(12)%ad %Cgreen%<(8)%aN%Cred%d %Creset%s" --date=relative --decorate --graph --all

* view repo logs only up to your current HEAD:

 git log --pretty=format:"%C(yellow)%h %Cblue%>(12)%ad %Cgreen%<(8)%aN%Cred%d %Creset%s" --date=relative --decorate --graph

* view all the changes that was made on a file.

 git log --patch <file or dir path>

* see entire file content at specific commit.

 git show <REVISION>:/path/to/file
 git show <REVISION>:/path/to/file > save_to_file

== Remotes ==

* '''list''' current remotes:

 # shows list of remote names and URL you have configured.
 git remote -v

* '''view''' remote detail:

 # this shows all local branches' which will pull/push from this remote by default.
 git remote show <remote_name>

 e.g.
 
 > git remote show origin

* '''add''' a new remote to local git repo:

 git remote add <remote_name> <remote_url>

 e.g.
 
 > git remote add upstream https://github.com/espressif/esp-idf.git

* '''create new branch''' and '''set default remote''' on the branch:

 git branch <new branch name> <rev to branch off>
 git checkout <new branch name>
 git push --set-upstream <remote name> <new branch name>
 
 # after this your push/pull command will sync with specified remote and remote branch.
 # if you have submodules in your git you might have to run these commands after checkout:
 
 git submodule update --init --recursive
 git clean -dff .

 e.g.
 
 > git branch test origin/master
 > git checkout test
 > git push --set-upstream origin test
 
 if you have submodules:
 
 > git submodule update --init --recursive
 > git clean -dff .

* '''view current branch default remote''':

 # show current branch default remotes.
 git status -sb
 
 # show all local branch default remotes.
 git branch -vv

* '''change default remote for specific branch''':

 # you can run this command without checking out the local branch.
 git branch --set-upstream-to=<remote name>/<remote branch name> <local branch name>

 e.g.
 
 # this will set local '''master''' branch to track remote branch '''master''' located in remote named '''upstream'''.
 > git branch --set-upstream-to=upstream/master master

== Submodules ==

* '''clone''' a project with all submodules together at once:

 git clone --recursive <git repo location> [<target dir>]

 e.g.
 
 > git clone --recursive git@repos.blah.com:blah6/frame.git
 
 or
 
 > git clone --recursive git@repos.blah.com:blah6/frame.git /scratch/myframe_git_repo

* '''clone''' a project without submodules then bring down all submodules:

 git clone <git repo location> [<target dir>]
 cd <local git repo dir>
 git submodule update --init --recursive

 e.g.
 
 > git clone git@repos.blah.com:blah6/frame.git
 > cd frame
 > git submodule update --init --recursive

* '''add new submodule''' to your main project. always execute this from your main git repo project top directory. this is because your submodule can have more submodules and you want to modify submodule on your main project and not in one of your submodule's project:

 cd <main project repo top dir>
 git submodule add <git repo location> <target dir>
 # if you want to checkout specific branch pass in option with -b <branch name>
 # git submodule add -b <branch name> <git repo location> <target dir>
 git submodule update --init --recursive
 # after adding new submodule you should 'git checkout -f <commit id or tag>' to some stable revision.

 e.g.
 
 > cd frame
 > git submodule add https://github.com/espressif/esp-idf.git ./external_modules/esp-idf
 > git submodule update --init --recursive
 > git commit -m "add esp-idf submodule." .
 > git push

* '''remove submodule''' from your main project. always execute this from your main git repo project top directory. this is because your submodule can have more submodules and you want to modify submodule on your main project and not in one of your submodule's project:

 cd <main project repo top dir>
 git rm -r <submodule directory path without trailing slash>
 # this should be deleted. this might be a bug. just remove manually.
 rm -rf .git/modules/<submodule directory path without trailing slash>

 e.g.
 
 > cd frame
 > git rm -r external_modules/esp-idf
 > git commit -m "remove esp-idf submodule." .
 > git push
 > rm -rf .git/modules/external_modules/esp-idf

* '''pull updates''' made to your submodules by someone else:

 cd <main project repo top dir>
 git pull
 git submodule update --init --recursive
 # sometimes this can leave left over directories/files. to clean left over directories/files.
 # running git status will show you if there are left over files.
 # must run 'git clean' for each submodule project with left over dirs/files.
 git status
 cd <submodule project top dir>
 git clean -dff .

 e.g.
 
 > cd frame
 > git pull
 > git submodule update --init --recursive
 > git status
 > cd external_modules/esp-idf
 > git clean -dff .

* '''modify submodule HEAD pointer''' and save. submodule project code should NOT be modified from this main project. from here, we only change HEAD pointer of where our submodule project points to. if you have to modify submodule project code checkout that code as stand alone project and modify it there:

 cd <one of your submodule project top dir>
 git checkout -f <specific commit ID or TAG>
 # if submodule also has submodules:
 git submodule update --init --recursive
 git clean -dff .
 # if submodule's submodule also need cleanup, will have to run clean from that directory.

 e.g.
 
 > cd external_modules/esp-idf
 > git checkout -f tag_v2.0
 > git submodule update --init --recursive
 > git clean -dff .
 > cd ../..
 > git commit -m "update esp-idf HEAD to tag 2.0." .
 > git push

* '''fetch''' all updates for submodules. when using submodules, only some specific commit revision might work with your main project. so you cannot blindly update all your submodules to the latest revision. do "git fetch" first then checkout specific revision for each submodule after testing it:

 cd <main project repo top dir>
 git fetch --all --tags --prune --recurse-submodules
 # now all your submodule will have up to date git logs.
 # check each submodule and checkout different commit revision if there are no breaking changes with your main project.
 
 # if you find a submodule that needs to be updated, checkout a different commit revision and commit changes into your main project.
 # follow "modify submodule HEAD pointer" instruction above.

== Merge and Rebase ==

 git rebase --onto <newparent> <oldparent> <until>
 
 newparent: attach here.
 oldparent (exclusive): take from this commit excluding this one.
 until (inclusive): take until this commit including this one.

when running this command use actual commit sha values.
after running this you will be in detached state, so create branch and commit.

== Revert/Cherry Pick Commit ==

== Tagging ==

== Stashing ==

== Partial Commit ==

== Set Repo Author's '''name''' and '''email''' ==

-->
