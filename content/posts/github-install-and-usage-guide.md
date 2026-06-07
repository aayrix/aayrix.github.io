+++
date = '2026-06-05T10:00:00+05:00'
title = 'GitHub — Full Install & Usage Guide with All Commands'
description = 'Complete guide to installing Git, configuring GitHub, SSH keys, cloning repos, branching, committing, pushing, and collaborating on GitHub.'
tags = ['github', 'git', 'version-control', 'ssh', 'devtools']
categories = ['GitHub', 'Tutorials']
ShowToc = true
ShowReadingTime = true

[cover]
image = "/images/covers/github-cover.png"
alt = "GitHub and Git guide"
caption = "GitHub — Full Install & Usage Guide"
+++

<div class="post-logo">

![GitHub Logo](/images/logos/github.svg)
![Git Logo](/images/logos/git.svg)

</div>

GitHub is the world's largest platform for hosting and collaborating on code. This guide covers **installing Git**, **setting up your GitHub account**, **authentication**, and every essential command you'll use day to day.

> **My GitHub:** [github.com/aayrix](https://github.com/aayrix)

## Table of Contents

1. [Create a GitHub Account](#1-create-a-github-account)
2. [Install Git](#2-install-git)
3. [Configure Git Identity](#3-configure-git-identity)
4. [Authentication Methods](#4-authentication-methods)
5. [Clone & Create Repositories](#5-clone--create-repositories)
6. [Daily Git Workflow](#6-daily-git-workflow)
7. [Branching & Merging](#7-branching--merging)
8. [Working with Remotes](#8-working-with-remotes)
9. [Collaboration on GitHub](#9-collaboration-on-github)
10. [Useful Git Commands Cheat Sheet](#10-useful-git-commands-cheat-sheet)

---

## 1. Create a GitHub Account

1. Go to [github.com/signup](https://github.com/signup)
2. Enter your email, create a password, and choose a username
3. Verify your email address
4. Complete your profile at [github.com/settings/profile](https://github.com/settings/profile)

---

## 2. Install Git

### Arch Linux

```bash
sudo pacman -S git
```

### Debian / Ubuntu

```bash
sudo apt update
sudo apt install git -y
```

### Fedora

```bash
sudo dnf install git -y
```

### macOS

```bash
# Xcode Command Line Tools (includes Git)
xcode-select --install

# Or via Homebrew
brew install git
```

### Windows

Download from [git-scm.com/download/win](https://git-scm.com/download/win) and run the installer.

### Verify

```bash
git --version
# git version 2.43.0
```

---

## 3. Configure Git Identity

Set your name and email (must match your GitHub account email for commit attribution):

```bash
git config --global user.name "Aayrix"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Set default editor
git config --global core.editor vim

# Enable colored output
git config --global color.ui auto

# View all config
git config --list
```

---

## 4. Authentication Methods

GitHub requires authentication to push code. Choose one method:

### Method A: SSH (recommended)

#### Generate SSH key

```bash
# Generate Ed25519 key (recommended)
ssh-keygen -t ed25519 -C "your.email@example.com"

# Press Enter to accept default path (~/.ssh/id_ed25519)
# Enter a passphrase (optional but recommended)

# Start SSH agent
eval "$(ssh-agent -s)"

# Add key to agent
ssh-add ~/.ssh/id_ed25519

# Copy public key to clipboard
cat ~/.ssh/id_ed25519.pub
```

#### Add key to GitHub

1. Go to [github.com/settings/keys](https://github.com/settings/keys)
2. Click **New SSH key**
3. Paste your public key
4. Click **Add SSH key**

#### Test SSH connection

```bash
ssh -T git@github.com
# Hi aayrix! You've successfully authenticated...
```

### Method B: HTTPS with credential helper

```bash
# Cache credentials for 8 hours
git config --global credential.helper cache

# Or store permanently (less secure)
git config --global credential.helper store

# Use a Personal Access Token instead of password
# Create at: https://github.com/settings/tokens
```

### Method C: GitHub CLI

```bash
# Install gh (see GitHub CLI guide)
gh auth login
gh auth setup-git
```

---

## 5. Clone & Create Repositories

### Clone an existing repo

```bash
# SSH
git clone git@github.com:aayrix/my-blog.git

# HTTPS
git clone https://github.com/aayrix/my-blog.git

# Clone into specific folder
git clone git@github.com:aayrix/my-blog.git my-blog
cd my-blog
```

### Create a new repo from scratch

```bash
# Create project folder
mkdir my-project && cd my-project

# Initialize Git
git init

# Create a README
echo "# My Project" > README.md

# Stage and commit
git add README.md
git commit -m "Initial commit"

# Create repo on GitHub and push
gh repo create my-project --public --source=. --remote=origin --push

# Or manually add remote
git remote add origin git@github.com:aayrix/my-project.git
git push -u origin main
```

### Create repo on GitHub (browser)

1. Go to [github.com/new](https://github.com/new)
2. Enter repository name
3. Choose public or private
4. Click **Create repository**
5. Follow the push instructions shown

---

## 6. Daily Git Workflow

The basic cycle: **modify → stage → commit → push**

```bash
# Check status of your files
git status

# See what changed
git diff

# Stage specific files
git add README.md
git add src/main.py

# Stage all changes
git add .

# Commit with message
git commit -m "Add user authentication feature"

# Push to GitHub
git push

# Pull latest changes
git pull
```

### View history

```bash
# Compact log
git log --oneline

# Last 5 commits
git log --oneline -5

# Show a specific commit
git show abc1234
```

### Undo changes

```bash
# Discard unstaged changes to a file
git checkout -- filename.txt

# Unstage a file (keep changes)
git reset HEAD filename.txt

# Amend last commit message
git commit --amend -m "Better commit message"

# Undo last commit (keep changes staged)
git reset --soft HEAD~1
```

---

## 7. Branching & Merging

```bash
# List branches
git branch

# Create and switch to new branch
git checkout -b feature/login-page

# Or (Git 2.23+)
git switch -c feature/login-page

# Switch to existing branch
git switch main

# Merge branch into current branch
git merge feature/login-page

# Delete branch after merge
git branch -d feature/login-page

# Push branch to GitHub
git push -u origin feature/login-page
```

### Stash changes temporarily

```bash
# Save work in progress
git stash

# List stashes
git stash list

# Apply and remove stash
git stash pop

# Apply without removing
git stash apply
```

---

## 8. Working with Remotes

```bash
# View remotes
git remote -v

# Add a remote
git remote add origin git@github.com:aayrix/my-project.git

# Change remote URL
git remote set-url origin git@github.com:aayrix/new-name.git

# Fetch without merging
git fetch origin

# Pull with rebase (cleaner history)
git pull --rebase origin main

# Push to specific branch
git push origin feature/my-branch

# Force push (use carefully!)
git push --force-with-lease origin main
```

---

## 9. Collaboration on GitHub

### Fork and contribute

```bash
# Fork a repo (via gh CLI)
gh repo fork torvalds/linux --clone=true
cd linux

# Add upstream remote
git remote add upstream git@github.com:torvalds/linux.git

# Sync with upstream
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

### Pull Requests

```bash
# Create PR via CLI
gh pr create --title "Fix typo in README" --body "Fixed spelling error"

# List PRs
gh pr list

# Review PR
gh pr review 5 --approve
```

### Issues

```bash
# Create issue
gh issue create --title "Add dark mode" --label enhancement

# List issues
gh issue list --state open
```

### GitHub Pages

```bash
# Enable Pages for a repo
gh api repos/aayrix/aayrix.github.io/pages -X POST \
  -f build_type=workflow \
  -f source[branch]=master \
  -f source[path]=/
```

---

## 10. Useful Git Commands Cheat Sheet

```bash
# ─── Setup ───────────────────────────────────
git config --global user.name "Your Name"
git config --global user.email "you@email.com"

# ─── Create & Clone ──────────────────────────
git init
git clone git@github.com:user/repo.git

# ─── Daily Workflow ──────────────────────────
git status
git add .
git commit -m "message"
git push
git pull

# ─── Branches ────────────────────────────────
git branch                    # list branches
git switch -c new-branch      # create + switch
git merge branch-name         # merge branch
git branch -d branch-name     # delete branch

# ─── History ─────────────────────────────────
git log --oneline
git diff
git show <commit>

# ─── Remote ──────────────────────────────────
git remote -v
git fetch
git pull --rebase

# ─── Undo ────────────────────────────────────
git checkout -- file          # discard changes
git reset HEAD file           # unstage
git stash                     # save for later
git commit --amend            # fix last commit

# ─── Tags ────────────────────────────────────
git tag v1.0.0
git push origin v1.0.0
```

---

## Next Steps

| Topic | Guide |
|-------|-------|
| GitHub CLI (`gh`) | [GitHub CLI Complete Guide](/posts/github-cli-complete-guide/) |
| Build a static site | [Hugo Pages Guide](/posts/hugo-pages-complete-guide/) |
| Install Linux | [Arch Linux Guide](/posts/install-arch-linux-complete-guide/) |

## Connect on GitHub

Follow my projects and contribute:

**[github.com/aayrix](https://github.com/aayrix)**

Git becomes second nature with practice. Start with `git init`, make small commits often, and push regularly. The GitHub workflow — branch, commit, PR, merge — is the foundation of modern software development.
