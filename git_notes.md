---
provider: Corey Schafer
tags: l&g, git, coding, youtube
---
# Command Line Fundamentals
- Pro Gi Book by Scott Chacon and Ben Straub
- git-scm.com/documentation
- video link: https://www.youtube.com/watch?v=HVsySz-h9r4&list=PL-osiE80TeTuRUfjRe54Eea17-YfnOOAx

## Initial Setup
- `git config --global user.name "Dorian Bates"`
- `git config --global user.email "dorianbates@gmail.com"`
- See config: `git config --list`

## Help
- git help < verb >
- git < verb > help

## Set Default Branch Name
Change default branch of `git init`
- `git config --global init.defaultBranch <branchName>`

- to stop tracking just `rm -rf .git` in the directory

## Status
- `git status` - Shows branch you're on, commits, and changed ready to be comitted
	![[Pasted image 20220807135419.png]]

## gitignore
- `touch .gitignore`
- creates a file that you can put in the names of files you want git to ignore.
- Good for config and local files that are part of a larger project.
- You can use wild card chars as well eg `*.pyc`

## 3 States
- **Working directory**
	- Untracked and modified files are here
	- Can see these in `git status`
- **Staging Area**
	- Where we organize what we want committed
	- You can stage individual files and stage chunks in multiple commits 
- **Committed Files**
	- Files that are part of a commit
	- You can have multiple commits in this area

### Add Files to a Staging Area
- Add all: `git add -A`
- Individually: `git add .gitignore`

### Remove Files from a Staging Area
- `git reset <filename>`
- `git reset` - removes everything from staging

## Commit Process
1. **Add files to staging area** - `git add -A` or `git add <files>`
2. **Check what's in staging** - `git status`
3. **Commit changes from staging** - `git commit -m "<commit message>"`

- If you run `git status` again after committing all changes you'll see "working directory clean"

## Logs
- `git log` will show a list of all commits

## Working with Remote Repos
- `git clone <url> <where to clone>`
	- eg: `git clone https://github.com/CoreyMSchafer/Sample-Repo.git .

### Viewing Info About Remote Repos
- `git remote -v` - Lists information about the repo that is local to your machine
- `git branch -a` - Lists all of the branches (local and remote) in the repo

### Pushing Changes
1. Commit changes locally:
	1. `git diff`
	2. `git status`
	3. `git add -A OR <filename>`
	4. `git commit -m "commit message"`
2. Then Push:
	1. `git pull origin master/main` 
		- pulls any changes from remote that may have happened while we were working on our local files
		- Options
			- `git config pull.rebase false` - (default strategy) local changes are merged with remote changes. Results in a merge commit that points to the latest local commit and the latest remote commit.
			- `git config pull.rebase true` - local changes are reapplied on top of the remote changes
		- **BEST PRACTICE** - Always rebase your local commits when you pull before pushing them.
		- **SET -REBASE AS DEFAULT** - `git config --global pull.rebase true`
	1. `git push origin master/main` 

## Common Workflow
### Initial Work
1. Create a branch for desired feature
	- `git branch <branch name>`
3. Checkout the Branch to start working on it
	- `git checkout <branch name>`
4. Do the work needed on the branch
5. `git status` - to see changes ready for staging
6. `git add <files>` - to stages desired changes
7. `git commit -m "commit message"`

### Push Branch to Remote
1. `git push -u origin <branch name>` - u option tells git to associate local branch to a remote branch.
	- This only needs to be done the first time the new local branch is pushed up to the remote repo
2. `git banch -a` - to show all branches remote/local

### Merge Branch
**NB: This part is usually done on the remote branch in most dev shops**
1. `git checkout main/master` - checkout local master branch
2. `git pull origina main/master` - pull down any remote changes
3. `git branch --merged` - shows a list of branches that have already been merged in.
	- This is good if you've merged multiple commits into your local main branch before pushing everything back up to the remote repo
3. `git merge <branch name>` - merges the named branch into the branch you're currently in. In this flow it's main since we switched to main the first step.
4. `git push origin main/master` - push the merged branch to remote repo

### Deleting A Branch
1. `git branch --merged` - to see list of branches whose changes have been merged into main
2. `git branch -d <branch to delete>` - to delete branch LOCALLY
3. `git branch -a` - to see your changes
4. `git push origin --delete learning_git` - to delete remote branch

# Fixing CommON Mistakes and Undoing Bad Commits
video link: https://www.youtube.com/watch?v=FdZecVxzJbk&list=PL-osiE80TeTuRUfjRe54Eea17-YfnOOAx&index=2

## Getting Rid of All Changes
- use the checkout command on the file or entire branch you want to wipe changes from
- `git checkout <file or branch>`
- You can verify changes are wiped by using `git status` or `git diff`

## Fixing Commit Messages
- After a commit you want to modify the message because you made a mistake
- `git commit --amend -m "revised message"`
- This will amend the previous commit
- This will change the commit hash because the message is part of the commit which the hash is created from
- **NB: This fix is only good for local fixes. If the commit has been pushed to remote then it will mess up the hash and other people's work**

## Missed Adding a File to a Commit
1. `git add <missed file>`
2. `git commit --amend`
	- This brings up an interactive window to edit you simply save and quit out of

`git log --stat` - shows files changed from commit

## Commits on the Wrong Branch
- Example: you need to move a commit from one branch to another.
	- You want to move the commit from Main to Feature branch
	- Restore Main back to where it was before the incorrect commits

### git cherry-pick
- Creates a new commit based off the original
- Only creates a new commit based off of an original, it will not delete the original

1. `git log` - to get the hash of the commit you want to cherry pick
2. Copy the hash
3. `git checkout <desired branch>` - switch to the branch you actually want to apply the commit to
4. `git cherry-pick <HASH>` - This will bring the commit over to the desired branch
5. Now we need to get rid of the commit on the branch we mistakenly applied it to:
	1. `git checkout <branch to reset>` - Move to the branch you need to fix
	2. 3 Types of reset:
		- `git reset --soft <hash of commit to go back to>` - will reset back to the commit specified. But will keep changes in the staging directory
		- `git reset <hash of commit to go back to>` - MIXED reset. It's the DEFAULT so you don't need to call the flag. Keeps the changes but they are moved from staging to the working directory.
		- `git reset --hard <hash of commit to go back to>` - make all of the tracked files match the state they were in based on the hash specified.
			- This will remove all changes which can be dangerous!
			- This will leave any untracked files along like: `.gitignore`
		- `git clean -df` - gets rid of untracked files. -d gets rid of any untracked directories. -f gets rid of any untracked files.
			- Good to know if a ton of files and directories are created like if you accidetally unziped a file in the project.

**What if you want to recover from** `git reset --hard` ?
- This is time sensitive. Git garbage will wipe after 30-ish days
- `git reflog` - shows a walkthrough of what you've done on a branch.
- You can grab the hash and run a `git checkout <hash>` will jump to the commit when you did a hard reset
- This will take you into a `detached head stat` so it's not on a branch.
- `git branch <backup>` to create a backup branch you can then commit with. You can call `backup` whatever you want

## Fixing Commits After Subsequent PUlls
- You want to revert commit history but other people have already pulled those changes and messing with the hash will create problems.
- `git revert` - creates new commits to revert the effects of earlier commits
- Won't modify or delete existing commits

1. Copy hash of desired commit
2. `git revert <hash>`
3. Will open a message to save and quit from
4. This creates a new commit with reverted changes

## Git Diff
- `git diff <hash1> <hash2>` - will show you the difference between 2 commits based on the commit hashes you give it.


# Git Stash Command
Video Link: https://www.youtube.com/watch?v=KLEDKgMmbBI&list=PL-osiE80TeTuRUfjRe54Eea17-YfnOOAx&index=3

- Whenever you have changes you're not ready to commit but you need to switch branches or you want to temporarily revert back but want to save changes.
- Will save your work in a temporarly place so you can come back to them later.

Example:
- You've worked on a file in a branch.
- You need to switch back to master to check something out or see what original file looked like.

1. `git stash save "save message"` - will stash all the changes you've made and then revert the working files to original state.
2. `git diff` at this point will not show anything
3. `git stash list` - will show all stashes and their unique id's
4. `git stash apply <stash@{ID}` - will bring you back to the specified stash. To get list of IDs use `git stash --list`
	- It picks up the changes in stash but leaves the stash
5. `git stash pop` - will pull the changes and then drop the stash. Will only grab the top stash on the heap

- You can stash multiple changes on the same files
- `git stash pop` will grab the stash on top

- `git stash drop stash@<ID>` - will drop the stash and revert files
- `git stash clear` - gets rid of all stashes and reverts working files

- A good way to save changes that you may have done on the wrong branch. Example: you made changes on Main but should have made them on Feature. 
- 
- 
- 
- You can stash the changes made on Main, checkout the Feature branch, and then reapply the stash to the Feature branch

# Diff and Merge Tools
Video Link: https://www.youtube.com/watch?v=iCGrKFH2oeo&list=PL-osiE80TeTuRUfjRe54Eea17-YfnOOAx&index=4