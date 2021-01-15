---
layout: post
title: Use multiple ssh-keys for different git accounts on the same computer
date: 2021-01-15 00:00:00 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: workflow.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [Productivity, Workflow] # add tag
---

At joining my current work place, I was asked to create a new GitHub account before I can be a member of the company’s GitHub organization and in turn get access to all the private project repositories.
It wasn’t until I left the work laptop in my locker but still needed to get something done that I felt the need to manage both of my GitHub accounts on my personal laptop (who needs work-life balance?)
To make things more complicated, the project I’m working on references other private repositories in its Cocoapods Specs dependencies block and needs to access them at `pod repo push` , which defaults to using my personal GitHub account’s ssh-key.
After some trials and failures, here’s how I finally got it to work:

### Create a new ssh-key and add it to the work GitHub account

```console
ssh-keygen -t rsa -b 4096 -C "my_work_email@my_company.com"
```

Say the new ssh-key was named “work_rsa”, now copy the content of the newly generated public key file (work_rsa.pub in this example) and paste it to the work GitHub account’s setting page as described in the GitHub help page.

### Modify the ssh config file ( `~/.ssh/config`)

Open the config file in a text editor (create it if there isn’t one in the ~/.ssh folder yet) and add the following to it:

```console
# Personal GitHub account
Host github.com
 HostName github.com
 User git
 AddKeysToAgent yes
 UseKeychain yes
 IdentityFile ~/.ssh/id_rsa
# Work GitHub account
Host github.com-work
 HostName github.com
 User git
 AddKeysToAgent yes
 UseKeychain yes
 IdentityFile ~/.ssh/work_rsa
 ```

### Clone the work project repo (with a slightly different address)

To clone the work project repo using the new ssh-key we need to tweak a little bit on the repo’s ssh address. The host url needs to match the Host defined in the ssh config file from last step, namely, where in the address there is github.com, replace it with github.com-work.

E.g., with the following private repo ssh address we get from GitHub:

>git@github.com:[my work GitHub group]/[my project].git

We need to tweak its address like this before we can `git clone` it:

>git@github.com-work:[my work GitHub group]/[my project].git

```console
git clone git@github.com-work:[my work GitHub group]/[my project].git
```
