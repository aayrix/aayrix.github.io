+++
date = '2026-06-03T10:00:00+05:00'
title = 'GitHub CLI (gh) — Complete Guide with Copy-Paste Commands'
description = 'Install GitHub CLI on Linux, authenticate with GitHub, and manage repos, issues, pull requests, and releases from the terminal.'
tags = ['github', 'github-cli', 'gh', 'terminal', 'devtools']
categories = ['GitHub', 'Tutorials']
ShowToc = true
ShowReadingTime = true

[cover]
image = "/images/covers/github-cover.png"
alt = "GitHub CLI guide"
caption = "GitHub CLI — Complete Command Reference"
+++

<div class="post-logo single">

![GitHub Logo](/images/logos/github.svg)

</div>

The **GitHub CLI** (`gh`) lets you interact with GitHub directly from your terminal — create repos, open PRs, review issues, and more without leaving the command line. This guide covers installation, authentication, and the most useful commands.

> **Prerequisites:** A [GitHub account](https://github.com/signup) and Git installed on your system.

## Table of Contents

1. [Install GitHub CLI](#1-install-github-cli)
2. [Authenticate with GitHub](#2-authenticate-with-github)
3. [Verify Your Setup](#3-verify-your-setup)
4. [Repository Commands](#4-repository-commands)
5. [Issues & Pull Requests](#5-issues--pull-requests)
6. [GitHub Actions & Releases](#6-github-actions--releases)
7. [Configuration & Tips](#7-configuration--tips)

---

## 1. Install GitHub CLI

### Arch Linux

```bash
sudo pacman -S github-cli
```

### Debian / Ubuntu

```bash
(type -p wget >/dev/null || (sudo apt update && sudo apt install wget -y)) \
  && sudo mkdir -p -m 755 /etc/apt/keyrings \
  && wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
  && sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
  && sudo apt update \
  && sudo apt install gh -y
```

### Fedora

```bash
sudo dnf install gh
```

### macOS (Homebrew)

```bash
brew install gh
```

### Verify installation

```bash
gh --version
```

---

## 2. Authenticate with GitHub

```bash
# Start interactive login
gh auth login
```

Follow the prompts:

```
? What account do you want to log into? GitHub.com
? What is your preferred protocol for Git operations? HTTPS
? Authenticate Git with your GitHub credentials? Yes
? How would you like to authenticate? Login with a web browser
```

Copy the one-time code shown, press Enter to open the browser, paste the code, and authorize.

### Authenticate with a token (CI / headless)

```bash
# Create a token at: https://github.com/settings/tokens
echo "ghp_YOUR_TOKEN_HERE" | gh auth login --with-token
```

### Check auth status

```bash
gh auth status
```

Expected output:

```
github.com
  ✓ Logged in to github.com account aayrix (keyring)
  ✓ Git operations for github.com configured to use https://github.com
  ✓ Token: gho_************************************
```

---

## 3. Verify Your Setup

```bash
# View your GitHub profile
gh api user

# List your repositories
gh repo list

# View authenticated user
gh auth status
```

---

## 4. Repository Commands

### Create a new repository

```bash
# Public repo in current directory
gh repo create my-new-project --public --source=. --remote=origin --push

# Private repo
gh repo create my-private-project --private

# Clone an existing repo
gh repo clone aayrix/my-blog
```

### View and manage repos

```bash
# View repo info
gh repo view aayrix/my-blog

# Open repo in browser
gh repo view aayrix/my-blog --web

# Fork a repo
gh repo fork torvalds/linux --clone=true

# Delete a repo (careful!)
gh repo delete my-old-project --yes
```

### Sync with remote

```bash
# Fetch and merge from upstream
gh repo sync

# Set upstream for a fork
gh repo set-default aayrix/my-blog
```

---

## 5. Issues & Pull Requests

### Issues

```bash
# List open issues
gh issue list

# Create an issue
gh issue create --title "Bug: navbar broken on mobile" --body "Steps to reproduce..."

# View a specific issue
gh issue view 42

# Close an issue
gh issue close 42

# Comment on an issue
gh issue comment 42 --body "Fixed in latest commit."
```

### Pull Requests

```bash
# List open PRs
gh pr list

# Create a PR from current branch
gh pr create --title "Add new blog posts" --body "Added 5 tutorial posts"

# Create PR with reviewers
gh pr create --title "Feature: dark mode" --reviewer aayrix

# View a PR
gh pr view 7

# Check out a PR locally
gh pr checkout 7

# Merge a PR
gh pr merge 7 --squash --delete-branch

# Review a PR
gh pr review 7 --approve --body "Looks good!"
```

### Check PR status in CI

```bash
gh pr checks 7
gh pr status
```

---

## 6. GitHub Actions & Releases

### Workflow runs

```bash
# List recent workflow runs
gh run list

# Watch a run in real time
gh run watch

# View run logs
gh run view 12345678 --log

# Re-run a failed workflow
gh run rerun 12345678
```

### Releases

```bash
# List releases
gh release list

# Create a release
gh release create v1.0.0 --title "v1.0.0" --notes "First stable release"

# Upload assets to a release
gh release upload v1.0.0 ./build/app.zip

# Download release assets
gh release download v1.0.0
```

---

## 7. Configuration & Tips

### Set default editor

```bash
gh config set editor vim
# or
gh config set editor "code --wait"
```

### Useful aliases

```bash
# Add custom aliases
gh alias set pv 'pr view'
gh alias set pc 'pr create'
gh alias set il 'issue list'

# Use them
gh pv 7
gh il
```

### View all config

```bash
gh config list
```

### SSH instead of HTTPS

```bash
gh auth login
# Choose SSH when prompted, then:
gh auth setup-git
```

### Quick reference cheat sheet

```bash
gh repo create <name>     # Create repo
gh repo clone <owner/repo> # Clone repo
gh pr create              # Open pull request
gh pr merge <number>      # Merge PR
gh issue create           # Create issue
gh run list               # List CI runs
gh release create <tag>   # Create release
gh auth status            # Check login status
```

---

## Connect with My GitHub

I use `gh` daily for managing repos and deployments. Check out my projects:

**[github.com/aayrix](https://github.com/aayrix)**

For more on Git basics and SSH setup, see the [GitHub Install & Usage Guide](/posts/github-install-and-usage-guide/).
