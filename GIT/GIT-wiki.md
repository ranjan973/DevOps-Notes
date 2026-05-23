# GIT Introduction and commands

### What is git?

GIT is a distributed version control and Source Code management (SCM) tool which solves the problem of versioning and collaboration in the software development. It works very similar to Linux and was developed by Linus Torvalds in 2005.

- Track file version
- Document History
- Manage Branches ( A branch is seperate line of development where you can work without effecting the main and working code)
- Provide Merging solutions
- Identify conflicts while merging and help resolve them. ( Conflict happens when same line or part of the code was modified differently in 2 branches)

### Installing GIT on Linux Distros
   #### Debian/Ubuntu:
   - sudo apt-get update
   - sudo apt-get install git
   - git --version OR git -v

  #### Fedora/RHEL:
  - `sudo dnf install git`

Config User Name and User Email in Git so that all the commits are named

`git config --global user.name "Anand Ranjan"`

`git config --global user.email "anand.ranjan@fisglobal.com"`

### How does GIT work under the hood?

Its a persistent map which uses key value pair to store the content. The value is the content (Ex: "Apple Pie") & the key to it is the SHA1 value of it. SHA is secure hash algorithm.

[aranjan@mycentosVM cookbook]$ `echo "Apple Pie" | git hash-object --stdin` --> Plumbing command
23991897e13e47ed0adb91a0082c31c82fe0cbe5

The first step to start working with Git is to create a repository using the git init command in the directory you want to track.

This initializes the directory as a Git repository by creating a hidden .git folder, which stores all the history, metadata, and configuration required for Git to track and manage changes in the project. The .git folder in a directory makes that folder a GIT repo

.git is the brain of the git repository.

<img width="504" height="578" alt="image" src="https://github.com/user-attachments/assets/8c6e75ee-70b4-41ff-8ea0-75e3f51b2f0c" />

`git init` - Creates a local repository in the folder it is executed from. This creates a .git hidden folder which is a key component of git and this is where all teh config and tracking and version history is kept. The GIT objects ( commits/files) are kept in the form of hashes ( usually SHA1)

`git status`: Very common git command which tell us the current branch we are in, which files are modified, which files are staged ( ready to be committed) and which files are untracked ( if any) that means, git is not tracking it yet. 

`git add` - git add moves changes to the staging area, preparing them for commit. It also starts tracking new files. The staging area is like a shopping cart where you put or add the file or folders which is ready to be committed.

`git log` - shows the commit history of the repo including who committed it and the message.
`git log <branchname>` shows the commit logs without actually switching to that branch.

`git commit` - To commit/save all the staged changes in the git repository. This creates a checkpoint or restore point and has a unique Hash ID.
`git commit -m "My first commit"` OR `git commit -am "Message"` ( To add to staging area and commit in the same command)

`git clone` : This is used to download a copy of the repository to your local machine.

**Diffrence between Cloning and Forking**: Clone downloads to your machine and can be achieved via git clone. Fork happens only to your remote repo like GitHub and there is no git command for it. You fork a repo to your github and then clone it to your local machine, make changes locally and then push to your fork. Then Open a Pull Request to the original repo.

`git cherry-pick <commit-ID or HASH>` - This merges a particular commit to the branch.

What is upstream change? Suppose I create a branch feature from main branch and someone committed something in the main branch which my feature branch is lacking. That's teh upstream change for the feature branch.

### Difference between GIT MERGE and GIT REBASE:

Both of these commands solve the same problem which is integrate changes from one branch to another branch - they just do it in different ways.
Suppose I created a new feature branch from main and did commits in the feature branch. Parallely main branch is also getting updated with new commits. If I want to bring the new commits from main to my feature branch, I have 2 options : `git merge` and `git rebase`. 

If I do `git merge main` from being in the feature branch, I will have extraneous merge commits coming from the main branch in the feature branch every time I need to incorporate upstream changes. If main is very active, this can pollute my feature branch's history quite a bit. This is the problem git rebase will solve.

`Git rebase` main moves the entire feature branch to begin on the tip of the main branch like the feature branch had branched from the updated main branch. The major benefit of rebasing is that you get a much cleaner project history. First, it eliminates the unnecessary merge commits required by git merge. Second, as you can see in the above diagram, rebasing also results in a perfectly linear project history—you can follow the tip of feature all the way to the beginning of the project without any forks. 

**IMP NOTE**: The rebase branch should be used in your own branch like feature for the history of the feature branch to look clean. It should be avoided in the shared branch since it rewrites history.
Think of git rebase like you are rebasing your feature branch with the upstream changes in the main without getting the commit messages of the main.

Look at https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing for more details.

### GIT Branching Strategy:

1) **Master/Main Branch** - This represents stable, production-ready code. It should always be clean, tested, and deployable. Only updated via merges from tested branches (feature/release/hotfix)

2) **Feature Branch** - A feature branch is created for any new feature or change (small or big). Development happens here, and once complete and tested, it is merged back into the main (or develop) branch.

3) **Release Branch** : A release branch is created to prepare the code for a production release. It is mainly used for testing, bug fixes, and final stabilization. No new features or active development are added and Only critical fixes are allowed. The release happens from this branch.

4) **Hotfix branch** : A hotfix branch is used to quickly fix critical issues in production without waiting for the next release and then it is merged to the master and release branches to keep everything in sync.

