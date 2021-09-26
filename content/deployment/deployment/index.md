---
title: Deployment
date: 2021-09-26T04:02:55.070Z
description: Deployment
---
# Deployment ⭐

::: warning
Before doing a major deployment _always_ download a backup of the site. If something goes wrong, you'll be able to revert to the previous version.
:::

## Deplybot for cPanel

To set up deploybot for a server on Ventra we need to do some preperations:

-   Create repo on bitbucket
-   Setup Ventra account for new site
-   Whitelist deploy ip on cpanel server. Send an e-ticket to ventra, ask them to whitelist deploybot ips. You can find ips from [this page](https://support.deploybot.com/article/96-ips-and-ports-for-firewall-setup)

### Connect Deploybot to repo

After logging in to deploybot, under erpositories click on "Connect a repositoty"

![connect to repo](./img/dev-guide-connect-repo.jpg)

Then we choose the account and select the repo we want to connect to

![Select the repo](./img/dev-guide-select-repo.jpg)

Then we connect the repo and giving it a title

![Connect the repo](./img/dev-guide-repo-connected.jpg)

Afterwards, deploybot will set up an environment for this repo which will take around 1-2 minutes.

### Setup Git on cPanel

Aftr logging to cPanel, type terminal on the top search and open it - alternatively navigate to Advanced->Termina. Don’t worry about the warning, just click the blue button.

![Open terminal](./img/dev-guide-terminal.jpg)

-   If you are not sure, where should you perforn the commands, just type **ls** and it shows you the list of folders in the root directory. You need to change the path to where the website files location shoul be. Most of time it should be in “/home/cpanel_login_name/public_html”
    The cmd is most likely: `cd /home/xxxxxx/public_html`. Note: if you use different folder you need to change “public_html” to your folder

-   Create git on server: `git init`

-   Add repo as a remote:
    git remote add origin git@bitbucket.org:yump/xxxxxx.git. Note: you can find this from bitbucket.

-   Connect between bitbucket and cpanel so the server can get code form bitbucket. To do this you need to create an SSH key on the server side and add to bitbucket. [Here is the reference](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/) - **DO NOT PUT PASSPHRASE**

Steps you need to take:

-   Create ssh key on the server - in terminal: ssh-keygen
-   You can find the generated key under /home/xxxxx/.ssh folder
-   Now you need to add this key to server ssh-agent.
    `eval`ssh-agent`ssh-add ~/.ssh/id_rsa`
-   Now you can add this public key to bitbucket. In terminal:
    cat /home/pathtoproject/.ssh/id_rsa.pub - copy this key.
-   Go to bitbucket and under Repository settings -> Access keys -> Add key and paste the key here.
-   In terminal test it and under your project folder type: `git fetch`

### Create connection between Deploybot and cPanel

In deploybot dashboard on the project panel , click on Add new server

![Add server](./img/dev-guide-add-server.jpg)

Then select shell on the next screen:

![Add shell](./img/dev-guide-shell.png)

On the next screen we will setup these settings:

Name could be “Staging” or “Production” depending on which environment you are creating.

Host: Server ip address which you can find in cpanel (shared IP address)

Port: 2683 (double check in ventra dashboard)

Username/ password: sever login which you can find in ventra dashboard under cPanel details

![Add userpass](./img/dev-guide-userpass.jpg)

Commands should be:

`git fetch --all
git checkout --force %COMMIT%`

Do NOT tick “Forward SSH key with read-only repository access to the server” option.

On the next screen  Deployment mode also depends on environment. If this is a staging environment, you can use “Automatic” so every time when you push code to repo it will automatically deploy to the server.
If this is a production environment, please use “Manual” because we need to test before deploy. We cn setup notify Slack which all messages will go to a single channel called "deploybot".

![Add settings](./img/dev-guide-extra.jpg)

## Using git for deployments

If you have SSH access to the remote server and it has git installed you can deploy a site using git.

Full instructions are at <https://trello.com/c/OlltJaqf/159-setting-up-git-deployment-functionality-on-remote-cpanel-server>

## Deployment checklists

During the final stages of a major project, a developer needs to go through the following checklists:

:white_check_mark: **Pre-Launch Checklist** - ensuring that all final tasks have been completed, and that hosting has been setup and tested. Usually done in the week(s) leading up to launch, designed to make launch day as smooth and stress-free as possible.

:white_check_mark: **Launch Checklist** - final changes to deploy the site live such as any final content migration and DNS changes to switch the site live. Done on launch day.

These checklists are both in Trello and the master versions live inside the [Dev Knowledgebase](https://trello.com/b/CXYeCCJr/yump-dev-knowledgebase). They can be copied from there into your project Trello board if they have not been already.

<blockquote class="trello-card"><a href="https://trello.com/c/4mOavYw0/129-pre-launch-checklist-2">Pre-Launch Checklist (2)</a></blockquote><script src="https://p.trellocdn.com/embed.min.js"></script>

<blockquote class="trello-card"><a href="https://trello.com/c/5xkIetiM/50-launch-checklist-1">Launch checklist (1)</a></blockquote><script src="https://p.trellocdn.com/embed.min.js"></script>

Feel free to suggest improvements and updates to these checklists within Trello.

### See also

-   [Hosting & DNS](../hosting-dns/hosting.md)
-   [Using SSH](ssh.md)
-   [DeployCat](https://github.com/SimonEast/DeployCat)
