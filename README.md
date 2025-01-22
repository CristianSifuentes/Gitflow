# Gitflow Workflow

## Table of Contents

1. [Overview of Gitflow](#overview-of-gitflow)
2. [Typical Branch Structure](#typical-branch-structure)
3. [Basic Commands & Workflow Examples](#basic-commands--workflow-examples)
    - [Setting up the Repository](#setting-up-the-repository)
    - [Starting a Feature](#starting-a-feature)
    - [Finishing a Feature](#finishing-a-feature)
    - [Creating a Release Branch](#creating-a-release-branch)
    - [Hotfix Branch](#hotfix-branch)
4. [Medium and Advanced Commands](#medium-and-advanced-commands)
    - [Rebase Interactive](#rebase-interactive)
    - [Stash Changes](#stash-changes)
    - [Cherry-Pick Commits](#cherry-pick-commits)
    - [Reflog](#reflog)
    - [Mergetool](#mergetool)
5. [Key Points to Remember](#key-points-to-remember)
6. [Conclusion](#conclusion)

---

## Overview of Gitflow

Gitflow is a branching model designed by Vincent Driessen that provides a systematic approach for managing releases, features, and hotfixes within a project. It is typically used by medium to large teams that require a structured development lifecycle.

### Main Branches

- **`master` (or `main`)**: Holds production-ready code. Each commit on `master` is typically tagged with a release version.
- **`develop`**: Integrates features for the next release. It always contains the latest code ready for further testing or staging (not production-ready).

### Supporting Branches

1. **Feature branches (`feature/...`)**: Used for developing new features or changes. Branched from `develop` and merged back into `develop` when complete.
2. **Release branches (`release/...`)**: Created from `develop` for preparing a group of features for a new release. Merged into both `master` and `develop`.
3. **Hotfix branches (`hotfix/...`)**: Used to quickly address critical issues in production. Branched from `master` and merged back into both `master` and `develop`.

---

## Typical Branch Structure

Here is a simplified view of Gitflow's branch structure:

```
(master)        *---------* (merge release to master)
                 \       
(develop)         *--*--*--*----* (develop)
                   \  \  \  \
(feature/xyz)        *--*---* (merge feature to develop)
(feature/abc)           *--*------*
(release/1.0.0)                *--*
(hotfix/1.0.1)                           *--*
```

---

## Basic Commands & Workflow Examples

### Setting up the Repository

```bash
git init
git checkout -b master    # Or main
git commit --allow-empty -m "Initial commit on master"

git checkout -b develop
git commit --allow-empty -m "Initial commit on develop"
git push -u origin master
git push -u origin develop
```

### Starting a Feature

```bash
git checkout develop
git pull origin develop   # Update local develop
git checkout -b feature/user-authentication

# Work on the feature...
git add .
git commit -m "Implement user login logic"

git push -u origin feature/user-authentication
```

### Finishing a Feature

```bash
git checkout develop
git pull origin develop   # Update local develop
git merge --no-ff feature/user-authentication
git push origin develop

git branch -d feature/user-authentication

git push origin --delete feature/user-authentication
```

### Creating a Release Branch

```bash
git checkout develop
git pull origin develop
git checkout -b release/1.0.0

git commit -am "Bump version to 1.0.0"
git push -u origin release/1.0.0

# Merge to master
git checkout master
git pull origin master
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Release 1.0.0"
git push --tags
git push origin master

# Merge back to develop
git checkout develop
git pull origin develop
git merge --no-ff release/1.0.0
git push origin develop

# Delete release branch
git branch -d release/1.0.0
git push origin --delete release/1.0.0
```

### Hotfix Branch

```bash
git checkout master
git pull origin master
git checkout -b hotfix/1.0.1

# Fix the bug
git commit -am "Fix critical auth bug"
git push origin hotfix/1.0.1

# Merge into master
git checkout master
git pull origin master
git merge --no-ff hotfix/1.0.1
git tag -a v1.0.1 -m "Fix auth bug"
git push --tags
git push origin master

# Merge into develop
git checkout develop
git pull origin develop
git merge --no-ff hotfix/1.0.1
git push origin develop

# Delete hotfix branch
git branch -d hotfix/1.0.1
git push origin --delete hotfix/1.0.1
```

---

## Medium and Advanced Commands

### Rebase Interactive

```bash
git checkout feature/xyz
git rebase develop
# Resolve conflicts if any...
git add .
git rebase --continue
```

Interactive rebase to squash commits:

```bash
git rebase -i HEAD~3
```

### Stash Changes

```bash
git stash
git checkout master
git pull origin master
git checkout feature/xyz
git stash pop
```

### Cherry-Pick Commits

```bash
git cherry-pick <commit-hash>
```

### Reflog

```bash
git reflog
# Find and restore a lost commit
git reset --hard <commit-hash>
```

### Mergetool

```bash
git mergetool
```

---

## Key Points to Remember

1. **Never commit directly to `master`** unless it’s a hotfix.
2. **Use feature branches** for new features or changes.
3. Always **merge release/hotfix branches back into both `master` and `develop`**.
4. Use **tags** for releases to mark production-ready code.
5. Ensure **commit messages are clear** and focus on a single change.
6. Use **Git hooks** or CI/CD pipelines to automate testing during merges.

---

## Conclusion

Gitflow is a structured branching model that excels when:
- You have multiple environments (e.g., development, staging, production).
- You follow well-defined release cycles.
- You have a larger team that benefits from separation of stable and in-progress code.

By following the Gitflow workflow and the commands outlined here, you can maintain an organized and efficient development process for your project.
