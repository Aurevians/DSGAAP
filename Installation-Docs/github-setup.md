# GitHub Setup Guide

> **Purpose:** Configure Git and GitHub on macOS for a personal GitHub
> account while keeping an existing work Git configuration isolated and
> untouched.

------------------------------------------------------------------------

## 📑 Document Index

1.  [Overview](#1-overview)
2.  [Environment Used in This Setup](#2-environment-used-in-this-setup)
3.  [Git Identity vs GitHub Username vs
    Authentication](#3-git-identity-vs-github-username-vs-authentication)
4.  [Recommended Directory
    Structure](#4-recommended-directory-structure)
5.  [Inspect the Existing Git
    Configuration](#5-inspect-the-existing-git-configuration)
6.  [Configure a Personal Git Identity for
    `~/Projects/`](#6-configure-a-personal-git-identity-for-projects)
7.  [Verify the Conditional Git
    Configuration](#7-verify-the-conditional-git-configuration)
8.  [Configure Repository-Specific Credential
    Handling](#8-configure-repository-specific-credential-handling)
9.  [Create a Fine-Grained Personal Access
    Token](#9-create-a-fine-grained-personal-access-token)
10. [Authenticate and Push to
    GitHub](#10-authenticate-and-push-to-github)
11. [Verify the Repository State](#11-verify-the-repository-state)
12. [Security Guidelines](#12-security-guidelines)
13. [Troubleshooting](#13-troubleshooting)
14. [Useful Git Commands](#14-useful-git-commands)
15. [Official References](#15-official-references)

------------------------------------------------------------------------

# 1. Overview

This guide documents the GitHub setup used for the DSGAAP repository on
macOS.

The setup was designed for a machine that is used for both work and
personal GitHub repositories.

The goal is to keep the two identities separate.

### Personal environment

``` text
~/Projects/
    ↓
Personal Git projects
    ↓
Git identity: MR
Email: rashmita.dba@gmail.com
GitHub account: Aurevians
```

### Work environment

``` text
~/repos/
    ↓
Work Git projects
    ↓
Git identity: Rashmita Kumari
Email: rbendapudi@guidewire.com
GitHub account: rbendapudi_gwre
```

> **Important:** The work repository directory was not modified as part
> of this setup.

------------------------------------------------------------------------

# 2. Environment Used in This Setup

### Operating system

macOS

### Personal workspace

``` text
/Users/rbendapudi/Projects/
```

### Work workspace

``` text
/Users/rbendapudi/repos/
```

### Personal GitHub account

``` text
Username: Aurevians
Profile name: MR
```

### Personal Git identity

``` text
Name: MR
Email: rashmita.dba@gmail.com
```

### Personal repository

``` text
https://github.com/Aurevians/DSGAAP.git
```

### Git credential helper

``` text
osxkeychain
```

The credential helper was provided by the Homebrew Git configuration:

``` text
/opt/homebrew/etc/gitconfig
```

------------------------------------------------------------------------

# 3. Git Identity vs GitHub Username vs Authentication

These are three different concepts and should not be confused.

## 3.1 Git commit identity

Configured with:

``` bash
git config user.name
git config user.email
```

For DSGAAP:

``` text
Name: MR
Email: rashmita.dba@gmail.com
```

This information is associated with Git commits.

------------------------------------------------------------------------

## 3.2 GitHub username

The GitHub account username is:

``` text
Aurevians
```

The GitHub profile name is:

``` text
MR
```

The profile name and username do not need to be the same.

------------------------------------------------------------------------

## 3.3 GitHub authentication

For HTTPS Git operations, GitHub can authenticate the account using a
Personal Access Token (PAT).

For this setup:

``` text
GitHub account: Aurevians
Authentication: Fine-grained Personal Access Token
```

GitHub documents that a personal access token is used in place of a
password for Git operations over HTTPS.

------------------------------------------------------------------------

# 4. Recommended Directory Structure

For a Mac used for both personal and work development, keeping the
projects physically separated makes the configuration easier to
understand and maintain.

``` text
/Users/rbendapudi/
│
├── Projects/                         # Personal
│   ├── DSGAAP/
│   ├── Python-Projects/
│   └── Future-Personal-Projects/
│
└── repos/                            # Work
    ├── WorkRepo1/
    ├── WorkRepo2/
    └── ...
```

The Git configuration is organized as:

``` text
~/Projects/*
    ↓
Personal Git identity

~/repos/*
    ↓
Existing work Git identity
```

------------------------------------------------------------------------

# 5. Inspect the Existing Git Configuration

Before modifying a work laptop, inspect the current configuration.

## 5.1 View the global Git configuration

``` bash
cat ~/.gitconfig
```

Example:

``` ini
[user]
    email = rbendapudi@guidewire.com
    name = Rashmita Kumari

[push]
    autoSetupRemote = true

[pull]
    rebase = true

[init]
    templateDir = /Users/rbendapudi/.git-templates

[core]
    hooksPath = /Users/rbendapudi/.git-hooks
```

### Important

Do not overwrite an existing work configuration just to configure a
personal repository.

------------------------------------------------------------------------

## 5.2 Check the credential helper

``` bash
git config --show-origin --get-regexp '^credential'
```

Example:

``` text
file:/opt/homebrew/etc/gitconfig    credential.helper osxkeychain
```

This shows both where the configuration came from and which credential
helper is being used.

------------------------------------------------------------------------

## 5.3 Check the effective Git identity

``` bash
git config user.name
git config user.email
```

To determine where the value came from:

``` bash
git config --show-origin --get user.name
git config --show-origin --get user.email
```

This is especially useful when multiple configuration files are
involved.

------------------------------------------------------------------------

# 6. Configure a Personal Git Identity for `~/Projects/`

Instead of changing the global work identity, use Git's conditional
configuration feature.

This allows repositories under `~/Projects/` to automatically use the
personal identity.

## 6.1 Create the personal Git configuration file

Create:

``` text
~/.gitconfig-personal
```

Command:

``` bash
cat > ~/.gitconfig-personal <<'EOF'
[user]
    name = MR
    email = rashmita.dba@gmail.com
EOF
```

Verify:

``` bash
cat ~/.gitconfig-personal
```

Expected:

``` ini
[user]
    name = MR
    email = rashmita.dba@gmail.com
```

------------------------------------------------------------------------

## 6.2 Add a conditional include

Add the following rule to the global Git configuration:

``` bash
git config --global includeIf.gitdir:~/Projects/.path ~/.gitconfig-personal
```

This creates an entry similar to:

``` ini
[includeIf "gitdir:~/Projects/"]
    path = /Users/rbendapudi/.gitconfig-personal
```

### What this means

When Git is working with a repository whose `.git` directory is under:

``` text
~/Projects/
```

Git loads:

``` text
~/.gitconfig-personal
```

Repositories outside that path continue to use the normal/global
configuration unless they have their own local configuration.

------------------------------------------------------------------------

# 7. Verify the Conditional Git Configuration

From the DSGAAP repository:

``` bash
cd ~/Projects/DSGAAP
```

Check:

``` bash
git config user.name
git config user.email
```

Expected:

``` text
MR
rashmita.dba@gmail.com
```

Then verify where Git obtained the settings:

``` bash
git config --show-origin --get user.name
git config --show-origin --get user.email
```

Expected:

``` text
file:/Users/rbendapudi/.gitconfig-personal    MR
file:/Users/rbendapudi/.gitconfig-personal    rashmita.dba@gmail.com
```

This confirms that the personal configuration is being loaded
conditionally.

------------------------------------------------------------------------

# 8. Configure Repository-Specific Credential Handling

When multiple GitHub accounts are used over HTTPS, GitHub documents
using the full repository URL when caching credentials.

For DSGAAP, this was configured locally so the setting does not modify
the work repositories.

Run from:

``` text
~/Projects/DSGAAP
```

``` bash
git config --local credential.https://github.com.useHttpPath true
```

Verify:

``` bash
git config --local --get credential.https://github.com.useHttpPath
```

Expected:

``` text
true
```

Verify where the setting is stored:

``` bash
git config --show-origin --get credential.https://github.com.useHttpPath
```

Expected:

``` text
file:.git/config    true
```

The resulting local configuration is similar to:

``` ini
[credential "https://github.com"]
    useHttpPath = true
```

### Why use `useHttpPath`?

Without it, Git may treat credentials for GitHub primarily by the host:

``` text
github.com
```

With it enabled, Git can distinguish credentials using the repository
path, which is useful when multiple GitHub accounts are used on the same
workstation.

> GitHub's current documentation describes this approach for managing
> multiple GitHub accounts over HTTPS.

------------------------------------------------------------------------

# 9. Create a Fine-Grained Personal Access Token

For the personal `Aurevians` account, a fine-grained Personal Access
Token (PAT) can be used for Git operations over HTTPS.

GitHub recommends fine-grained tokens when they fit the use case because
they can be restricted to specific repositories and permissions.

## 9.1 Navigate to GitHub

Sign in to the personal GitHub account:

``` text
Aurevians
```

Navigate to:

``` text
Settings
→ Developer settings
→ Personal access tokens
→ Fine-grained tokens
→ Generate new token
```

------------------------------------------------------------------------

## 9.2 Recommended token configuration for DSGAAP

Example:

``` text
Token name:
DSGAAP-Git
```

### Resource owner

``` text
Aurevians
```

### Expiration

Use an expiration date rather than an unnecessarily long-lived token.

The exact expiration should be chosen according to your security
requirements.

### Repository access

Select:

``` text
Only select repositories
```

Then select:

``` text
Aurevians/DSGAAP
```

### Repository permissions

For normal Git repository read/write operations:

``` text
Contents → Read and write
```

GitHub also requires the repository `Metadata` permission as read-only
for this type of repository access.

Do not grant additional permissions unless a future requirement
specifically needs them.

------------------------------------------------------------------------

## 9.3 Protect the token

GitHub displays the generated token only once.

**Never:**

-   commit the token to Git
-   place it in a README
-   place it in Python source code
-   place it in `.git/config`
-   paste it into chat
-   put it into screenshots
-   put it into shell scripts
-   put it into committed environment files

Treat the token like a password.

------------------------------------------------------------------------

# 10. Authenticate and Push to GitHub

After the token is generated, make sure the repository uses the correct
GitHub username:

``` bash
git config --local credential.username "Aurevians"
```

Verify:

``` bash
git config --local --get credential.username
```

Expected:

``` text
Aurevians
```

Then push:

``` bash
git push
```

Git may prompt:

``` text
Username for 'https://github.com':
```

Enter:

``` text
Aurevians
```

If Git asks for a password:

> Enter the **Personal Access Token**, not the normal GitHub account
> password.

Nothing may visibly appear while the token is entered. This is normal
for a password/token prompt.

------------------------------------------------------------------------

# 11. Verify the Repository State

After a successful push:

``` bash
git status
```

Expected:

``` text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

Verify the remote:

``` bash
git remote -v
```

Expected:

``` text
origin  https://github.com/Aurevians/DSGAAP.git (fetch)
origin  https://github.com/Aurevians/DSGAAP.git (push)
```

Verify the latest commit:

``` bash
git log -1 --oneline
```

------------------------------------------------------------------------

# 12. Security Guidelines

## 12.1 Never commit secrets

Never put credentials directly into source code:

``` python
API_KEY = "real-secret-value"
```

Instead, use appropriate secret/environment-variable mechanisms.

------------------------------------------------------------------------

## 12.2 Never commit PATs

A PAT must never appear in:

``` text
.py files
.md files
.ipynb files
.git/config
shell scripts
environment files that are committed
Git history
```

If a token is accidentally committed, treat it as compromised and
revoke/rotate it immediately.

------------------------------------------------------------------------

## 12.3 Use least privilege

Prefer:

``` text
One token
    ↓
One purpose
    ↓
Minimum repository access
    ↓
Minimum permissions
```

For the DSGAAP setup:

``` text
DSGAAP-Git
    ↓
Aurevians/DSGAAP only
    ↓
Contents: Read and write
```

------------------------------------------------------------------------

## 12.4 Use expiration

Shorter-lived credentials reduce the impact of accidental exposure.

When the token expires:

1.  Create a replacement token.
2.  Update the stored credential.
3.  Verify Git operations.
4.  Revoke the old token if appropriate.

------------------------------------------------------------------------

# 13. Troubleshooting

## 13.1 `403 Permission denied`

Example:

``` text
remote: Permission to Aurevians/DSGAAP.git denied to rbendapudi_gwre.
fatal: ... error: 403
```

Possible cause:

GitHub is authenticating using the wrong account.

Check:

``` bash
git config --local --get credential.username
```

For DSGAAP:

``` text
Aurevians
```

Also check:

``` bash
git config --local --get credential.https://github.com.useHttpPath
```

Expected:

``` text
true
```

------------------------------------------------------------------------

## 13.2 Git is not using the expected Git identity

Check:

``` bash
git config user.name
git config user.email
```

Then:

``` bash
git config --show-origin --get user.name
git config --show-origin --get user.email
```

For a repository under `~/Projects/`, the source should point to:

``` text
~/.gitconfig-personal
```

------------------------------------------------------------------------

## 13.3 `git pull` refuses because of unstaged changes

Example:

``` text
error: cannot pull with rebase: You have unstaged changes.
error: Please commit or stash them.
```

First inspect:

``` bash
git status
```

If the change is intentional, commit it.

If you want to temporarily set it aside:

``` bash
git stash
```

Then pull:

``` bash
git pull
```

Restore the stashed changes later:

``` bash
git stash pop
```

If the change should be discarded, use `git restore` only after
confirming the file does not contain work you need.

------------------------------------------------------------------------

## 13.4 A credential already exists in macOS Keychain

Git may use:

``` text
osxkeychain
```

to store HTTPS credentials.

Inspect the credential helper:

``` bash
git config --show-origin --get-regexp '^credential'
```

On this setup, the helper was:

``` text
file:/opt/homebrew/etc/gitconfig
credential.helper osxkeychain
```

Do not delete or modify a work credential blindly on a work-managed
laptop.

------------------------------------------------------------------------

# 14. Useful Git Commands

### Show current repository status

``` bash
git status
```

### Show configured remote

``` bash
git remote -v
```

### Show current Git identity

``` bash
git config user.name
git config user.email
```

### Show where a setting came from

``` bash
git config --show-origin --get user.name
git config --show-origin --get user.email
```

### Show repository-local configuration

``` bash
git config --local --list
```

### Show credential-related configuration

``` bash
git config --show-origin --get-regexp '^credential'
```

### Show current branch and recent commit

``` bash
git log -1 --oneline
```

### Download changes from GitHub

``` bash
git pull
```

### Upload local commits

``` bash
git push
```

------------------------------------------------------------------------

# 15. Official References

-   GitHub --- Managing multiple accounts:
    https://docs.github.com/en/account-and-profile/how-tos/account-management/managing-multiple-accounts

-   GitHub --- Managing personal access tokens:
    https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens

-   GitHub --- Permissions required for fine-grained personal access
    tokens:
    https://docs.github.com/en/rest/authentication/permissions-required-for-fine-grained-personal-access-tokens

------------------------------------------------------------------------

## 🌱 DSGAAP Learning Note

This setup demonstrates several important real-world engineering
concepts:

``` text
Git
│
├── Commit identity
│
├── Conditional configuration
│
├── Repository-local configuration
│
├── Credential helpers
│
├── HTTPS authentication
│
├── Personal Access Tokens
│
├── Least-privilege access
│
└── Multiple GitHub accounts on one workstation
```

The important lesson is not just memorizing the commands.

Understand **why** each configuration exists, **where** it is stored,
and **what scope** it affects.

> **A good development environment should make the right thing
> easy---and make accidental changes to the wrong environment
> difficult.**
