---
layout: post
title: "Personal Git cheat sheet for stuff I usually forget"
categories: jekyll update
date: 2023-07-06
published: true
---
Mostly just comprise of Git (and Github) related stuff. Will probably structure this as a flow from setting up Git all the way to push and PR (the usual workflow).

<br>

# 1. SSH Authentication
Source: [Official docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

Generating ssh public and private keys (and hit enter all the way).
```
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Activate agents, get the public key, and paste that to Github SSH (Setting &rarr; SSH and GPG keys).
```
eval "$(ssh-agent -s)"
cat ~/.ssh/id_25519.pub
```

<br>

# 2. Push changes from one branch to another branch
A neat little cool trick that my co-worker shown me.

```
git push <remote> <branch with new changes>:<branch you are pushing to> 
```
What it mean is that, push to **\<remote\>**, changes of **\<branch with new changes\>**, to the **\<branch you are pushing to\>**. A neat thing is that, Github will create that new branch you are pushing to if you don't have it. Handy for pushing changes from `dev` repo to `prod`.

<br>

# 3. Oh yea i forgot that moments

Do you ever commit then found out you miss some file in the commit, or some small changes?

```
git commit --amend --no-edit
```
This would include new added changes to the latest commit.

<br>

Feel free to message or PR me if you have any cool tricks.

