name: inverse
layout: true
class: center, middle, inverse

---
# Got to Git them all!

_Introcution to Git and VCS_

---
layout: false

### What will we talk about?
- What is a VCS
- Distributed systems
- Git specifics

.footnote[
.red[*] Disclamer: Git is the only worthwhile VCS; SVN/CVS sucks]

---
.left-column[
  ## What, why and how?
  ### - What is Git?
]
.right-column[

- Manages a changes of a tree of files over time

- Optimized for
    - Distributed development
    - Large file sets (Linux approx. 35k files)
    - Complex merges
    - Making trial branches, experimentation
    - Performance (extremly fast)
    - Being robust (corruption etc.)
    - Tracks content not files, very important
- Not optimized for
    - Tracking file permissions and ownership
    - Tracking individual files with separate history
]

---

.left-column[
  ## What, why and how?
  ### - What is Git?
  ### - Why Git?
]
.right-column[


- Essential to Linux kernel development

  - Replacement to BitKeeper as of the controversy

- Everybody has commit rights (most people are idiots)
- Anyone can

  - Clone
  - Make and test local changes
  - Submit patches or publish repository (pull requests and push/pull)
  - Track upstream to revise if needed

]

---

.left-column[
  ## What, why and how?
  ### - What is Git?
  ### - Why Git?
  ### - How does Git do it?
]
.right-column[


- Universal public identifiers (40 char sha1 hash)
- Multi-protocol transport
- Efficient object store (all data extremly efficient)

  - Repo-size = 1.5x Checkout of one version

- Everyone has entire repo (disk is cheap)
- Easy branching and merging

  - Common ancestors are computable

]

---

.left-column[
  ## Git under the hood
  ### - SHA1 is King
]
.right-column[

- Every object has a SHA1 to uniquely identify it
- Efficient storage, minimal duplication
- Objects consist of:

    - Blobs (contents of files)
    - Trees (directories of blobs or other trees)

- Commits:
    - A tree
    - Plus zero or more parent commits
    - Commit-message

- Tags

]

---

.left-column[
  ## Git under the hood
  ### - SHA1 is King
  ### - Objects live in the repo
]
.right-column[

- Git efficiently creates new objects
- Objects are generally added, not destroyed (we can have concurrent operations)
- Objects start _loose_ and then get _packed_

  - Object deltas, increase space efficiency

]

---

.left-column[
  ## Git under the hood
  ### - SHA1 is King
  ### - Objects live in the repo
  ### - Commits rule
]
.right-column[


- One or more commits form the head of object chains
- Typically one head called _master_
- New heads (branches) can be made at will
- All reachable repo objects are accessed from these head commits (leaf nodes in the tree)

  - Chase down the tree object to get to directories and files as they existed at this commit time
  - Chase down the parent objects to get to earlier commits and their respective trees

]

---

.left-column[
  ## Git under the hood
  ### - SHA1 is King
  ### - Objects live in the repo
  ### - Commits rule
  ### - Mapping objects to a file tree
]
.right-column[


- A _working tree_ has a _.git_ directory at the top level
- Unlike CVS, SVN: no pullution of deeper directories
- The _.git_ contains

  - config: Configuration file
  - objects/*: Object repository
  - refs/heads/*: branches (like _master_)

    - 41 bytes (hash + newline) -> extremly efficient branching

  - refs/remotes/*: Tracking remotes (remote branches)
  - index: index cache
  - HEAD: points to one of the branches (the current branch)
]

---

.left-column[
  ## Git under the hood
  ### - SHA1 is King
  ### - Objects live in the repo
  ### - Commits rule
  ### - Mapping objects to a file tree
  ### The index
]
.right-column[

- A directory of blob objects
- Represents the _next commit_
- Add files to put them in the index, including current content

  - We can selectivly choose which files (and even part of files) which we want to include in the next commit

- Commit takes the current index and makes it a real commit object
- Diff between HEAD and index

  - Changed things not yet commited

- Diff between index and working dir

  - Changed thrings not yet added
  - Untracked things

- Index gets interesting during a merge

]

---
## Git commands

- All git commands start with `git`
- Creating a repo

  - git init: create new repo in current directory
  - git clone: create a repo from an existing repo, creates a subdirectory

    - tracks remote branches to match local branches (so you can push and pull)

---

.left-column[
  ## Workflow
  ### - Typical workflow
]

.right-column[

- Edit edit edit
- git-add _changed files_: Adds files to the index
- git-status: reports the changes of index, working dir etc
- git-commit: (-m if you dont want a text editor)

  - Make sure to have good commit messages so you know what you did, see [How to write a commit message](http://chris.beams.io/posts/git-commit/)

- Current branch is moved forward
- Back to more edits
]

---

.left-column[
  ## Workflow
  ### - Typical workflow
  ### - Global and local
]

.right-column[
- Generally, your output is more commits
- These are always on a branch
- A branch is just a named commit
- When you commit, the former branch head becomes the parent
- The branched head moves to be the new commit
- Thus you're creating a directed acyclic graph, rooted in branch heads
- A merge is just a commit with multiple parents
]

---

.left-column[
  ## Workflow
  ### - Typical workflow
  ### - Global and local
  ### - Which branch
]

.right-column[
- Git encourages branching
- Typicsl work flow would also involve:

  - Thing of something to do
  - git-checkout -b topic-name
  - work work work, commit to topic-name

- When your thing is done:

  - git checkout master
  - git merge topic-name
  - git branch -d topic-name (will warn you if it is not merged)

- For all work as a single commit

  - git merge --squash --no-commit t; git commit
]

---

.left-column[
  ## Workflow
  ### - Typical workflow
  ### - Global and local
  ### - Which branch
  ### - Working in parallel
]

.right-column[
- You can have multiple topics active

  - git checkout -b topic1 master
  - work work work; commit; work work; commit
  - git checkout -b topic2 master
  - work work work; commit
  - git checkout topic1
  - work work; commit

- Decide how to bring them together: Merge or rebase

- Each has pros and cons

  - Merge: conflicts may arise, you may need to resolve by hand
  - Rebase: Rewrites commit as if they started in a different place

.footnote[
.red[*] THOU SHALL NOT REBASE IF THY HAS PUSHED
]

]

---
## Read the history

- git log: print the changes

---
## What is the difference?

- git diff: Diff between index and working tree
- git diff --staged: DIff between HEAD and index
- git diff OTHERBRANCH: other branch and working tree

---
## Playing well with others

- git clone creates tracking or remote branches
- Typically named origin/master etc
- To share you work first get up to date:

  - git-fetch or git-pull

- Now rebase your changes on upstream

  - git checkout master (or branch of interest)
  - git rebase origin/master

- To push upstream

  - git push

---
## Resetting

- git reset --soft: Makes all files updated but not checked in
- git resset --hard: Forces working dir to look like last commit **DANGER**
- git reset --hard HEAD~3: tosses most recent 3 commits
- git checkout some/lost/file: recover the version of some/lost/file from the last commit

---
## Other nice things to learn later on

- git bisect: find the offensive commit
- git cherry-pick: selective merging
- git blame: who wrote this?

---
## Getting Git?

- Preinstalled on Linux/Mac
- Google Git if on Windoes
- Many different types of GUIs, although I recommend the command line to learn better

  - GitKraken
  - Tower
  - SourceTree

---
name: last-page
template: inverse

## That's all folks (for now)!
