# Git Installation, Initialization & Configuration Guide

This guide provides step-by-step instructions for installing, configuring, and initializing Git.

## 1. Verify Git Installation

Check if Git is already installed:

```bash
git --version  # Check Git version
# or
which git      # Shows installation path
```

## 2. Install Git on Ubuntu

```bash
# Add Git PPA (for latest stable versions)
sudo add-apt-repository ppa:git-core/ppa -y
sudo apt update
```

```bash
# Fix potential dependency issues : if any
sudo apt --fix-broken install -y
```

```bash
# Install Git
sudo apt install git -y
```

## 3. Post-Installation Verification

```bash
# Expected output: git version 2.x.x
git --version
```

## 4. Configuration Settings

#### 4.1 Global Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global core.editor "nano"  # Set preferred text editor
```

#### 4.2 Local Configuration

```bash
git config --local user.name "Project Specific Name"
git config --local user.email "project.email@example.com"
```

## 5. Managing Configurations

View configurations :

```bash
git config --list              # All active settings
git config --global --list     # Global settings
git config --local --list      # Repository-specific settings
```

Remove local configurations :

```bash
git config --local --unset user.name
git config --local --unset user.email
```

## 6. Setting `main` as the Default Branch for New Repositories

```bash
git config --global init.defaultBranch main   # Set 'main' as default
git config --global --get init.defaultBranch  # Verify the change
git init --initial-branch=main                # Override per repository
```

> The option `init.defaultBranch` vailable from Git version 2.28+ only.

#### Change existing repository `master` branch name to `main`

```bash
git branch -m master main # Rename the Local master Branch
```

##### If it's already pushed to `remote`, then :

```bash
git push --set-upstream origin main # Set Upstream and push the New main Branch to Remote
git push origin --delete master # Delete the Old master Branch on Remote
git branch --delete master # Delete the master branch locally
git pull origin --prune # Clean Up References to master (Optional)
```

## 7. Cloning Repository Operations

#### 7.1 Using SSH (Recommended for Security)
```bash
git clone git@github.com:user/repo.git
```

#### 7.2 Using HTTPS with a Token (For Authentication)
```bash
git clone https://$GH_TOKEN@github.com/user/repo.git
```

#### 7.3 Using Standard HTTPS (Requires Authentication for Pushes)
```bash
git clone https://github.com/user/repo.git
```

## Best Practices:

1. Always configure user.name/user.email before making commits
2. Use SSH keys for secure authentication ([GitHub SSH Guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh))


## Additional Resources:

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [GitHub Documentation](https://docs.github.com/en/get-started)
- [Git Cheat Sheet](https://training.github.com/downloads/github-git-cheat-sheet/)