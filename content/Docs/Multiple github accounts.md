---
title: Managing Multiple GitHub Accounts on the Same Computer 
---

## Problem Statement 😐

You have two GitHub accounts: **user1** (private) and **user2** (work-related), and you want to use both accounts on the same computer without typing passwords every time you use Git.

## Solution Overview ✅

Use SSH keys with host aliases in an SSH config file and configure different `.gitconfig` user credentials based on your current working directory.

This will allow you to change github credentials based on what working directory you're in. 

## Step-by-Step Implementation 🌟

### 1. Navigate to Your Home Directory

Ensure you're in your home directory (`~`). You should already have a `.ssh` folder by default. Since it's a hidden folder, use `ls -a` to see it. If it doesn't exist, create it with:

```bash
mkdir ~/.ssh
```

### 2. Generate SSH Keys for Both Accounts

Navigate to your `.ssh` directory and run these commands:

```bash
ssh-keygen -t rsa -b 8192 -C "user1@email.com" -f "github-user1"
ssh-keygen -t rsa -b 8192 -C "user2@email.com" -f "github-user2"
```

**Command breakdown:**
- `-C` flag adds a comment to help identify the key
- `-f` flag specifies the filename for the key pair

For the passphrase, you can either add one or press Enter to skip it.

You should now have public and private keys for both accounts in your `~/.ssh/` directory.

### 3. Add SSH Keys to Your SSH Agent

```bash
ssh-add --apple-use-keychain ~/.ssh/github-user1
ssh-add --apple-use-keychain ~/.ssh/github-user2
```

To verify all entries have been added, use:

```bash
ssh-add -l
```

### 4. Create SSH Config File

If you don't have a config file, create one:

```bash
touch ~/.ssh/config
```

Add the following configuration to your `~/.ssh/config` file:

```
Host github.com-user1
    HostName github.com
    User git
    UseKeychain yes
    IdentityFile ~/.ssh/github-user1

Host github.com-user2
    HostName github.com
    User git
    UseKeychain yes
    IdentityFile ~/.ssh/github-user2
```

### 5. Add Public Keys to GitHub Accounts

Sign in to each GitHub account and add the corresponding public SSH key:

1. Go to **Settings** → **SSH and GPG Keys**
2. Copy the correct public keys by running:

```bash
pbcopy < ~/.ssh/github-user1.pub
pbcopy < ~/.ssh/github-user2.pub
```

3. Paste each key into the respective GitHub account

### 6. Test SSH Connections

Verify your connections by running:

```bash
ssh -T git@github.com-user1
ssh -T git@github.com-user2
```

You should receive a response similar to:

```
Hi user1! You've successfully authenticated, but GitHub does not provide shell access.
```

### 7. Clone Repositories

When cloning repositories, modify the clone URL by adding your username:

**Original GitHub URL:**
```
git clone git@github.com:AccountName/pathtoproject.git
```

**Modified URL for user1:**
```
git clone git@github.com-user1:AccountName/pathtoproject.git
```

**Modified URL for user2:**
```
git clone git@github.com-user2:AccountName/pathtoproject.git
```


> [!tip]
> Check your remote configuration using:
> ```bash
> git remote -v
> ```


### 8. Configure Git User Settings by Directory

To ensure the correct user configuration for each project, set up conditional includes in your global `.gitconfig`.

Add this to your global `.gitconfig` file (`~/.gitconfig`):

```
[includeIf “gitdir:~/code/private/“]
  path = ~/.gitconfig-private
[includeIf “gitdir:~/code/work/“]
  path = ~/.gitconfig-work
```

Now create the specific configuration files:

```bash
touch ~/.gitconfig-private
touch ~/.gitconfig-work
```

Add the following to `~/.gitconfig-private`:

```
[user]
    name = user1
    email = user1@email.com
```

Add the following to `~/.gitconfig-work`:

```
[user]
    name = user2
    email = user2@email.com
```


> [!tip]
> You can verify your current Git configuration by running:
> ```bash
> git config --list
> ```



This command displays both global and local Git configurations. Local configurations can be found in `.git/config` within each repository.

## Summary 🏁

You now have a complete setup for managing multiple GitHub accounts on the same computer. Each account will use its respective SSH key and user configuration based on your working directory.

## Troubleshooting

Common troubleshooting tips:
1. Verify SSH keys are properly added: `ssh-add -l`
2. Check SSH connections: `ssh -T git@github.com-user1`
3. Verify Git configuration: `git config --list`
4. Ensure correct host aliases in SSH config