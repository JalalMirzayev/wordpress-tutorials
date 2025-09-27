# Video Tutorial

[WordPress Staging & Deployment Guide (GitHub Actions Workflow)](https://www.youtube.com/watch?v=BrwdgJ0aGsA)

# Create all in One Backup

Before you start make sure your credentials are secure.

Go to Plugins and install and activate `All-in-One WP Migration` by ServMask (+ 5 Mio. Active Installations).

After installation you will have the `All-in-One WP Migration` menu point in your Wordpress admin left pane navigation. Select Export>Export Site To > File

## Subdomains for Development and Staging

If your website url is `homepage.com` you will have `dev.homepage.com` and `staging.homepage.com`.

## Uploading Backup

If you have a running instance of Wordpress. Go online and install a wordpress page.

Go to Plugins and install and activate`All-in-One WP Migration` by ServMask (+ 5 Mio. Active Installations).

Navigate to the Plugin and use import this time. Upload the backup file. Log in again with the credentials from your local webpage.

# Git integration

In your VS Code project create a folder `.github/workflows` and create a `deploy.yml` file. Paste the following content.

```yml
name: Publish Website

on:
  push:
    branches:
      - main

jobs:
  web-deploy:
    name: 🚀 Deploy Website Every Commit
    runs-on: ubuntu-latest
    steps:
      - name: 🚚 Get Latest Code
        uses: actions/checkout@v3

      - name: 📂 Sync Files
        uses: SamKirkland/web-deploy@v1
        with:
          target-server: live.learnwebcode.com
          remote-user: youruser
          private-ssh-key: ${{ secrets.SSH_KEY }}
          destination-path: /home/youruser/live.learnwebcode.com/wp-content/themes/my-awesome-theme/
```

Adapt your target-server, remote-user (from web hosting), private-ssh-key, and destination-path.

## In your web hosting platform activate Secure Shell Access (SSH)

From your terminal run and trust

```bash
ssh <username>@homepage.com
# And enter the password for this user
```

After the login was successful cd into your home directory.

```bash
mkdir .ssh
cd .ssh
touch authorized_keys
clear
```

Open a terminal on your device.

```bash
ssh-keygen -m PEM -t rsa -b 4096
# give it a new name to prevent overwriting your other keys e.g. <my_ssh_key>
# Provide a strong passphrase
```

You should have <my_ssh_key> (private) and <my_ssh_key>.pub (public). Cat the content of the public key and copy & paste it into terminal in your hosted account go into the authorized_keys file and paste the content.

Modify the permissions on this file with `chmod 700 .ssh && chmod 600 .ssh/authorized_keys`.

Now cat the private key (never share with anyone!!!) and copy & paste it as a GitHub secret.
Settings > Secrets and variables > Actions. Set up a secret `SSH_KEY` (same as in deploy.yml).

## Commit changes

```bash
git add .
git commit -m "added github action"
git push --set-upstream origin main
```

Back in github look at actions and see if the action was already registered.

# Staging

## Create a webpage

In your hosting web interface. Create a new website as a subdomain. We don't want to create a new user but use the previous user. Past in your user from the previous step. On this subdomain install wordpress.
Repeat the step from the All in one Migration.

## Staging branch

Pull the current state of the webpage.
Create a new branch `git checkout -b staging`. Modify the deploy.yml file.

```yml
name: Publish Website

on:
  push:
    branches:
      - staging

jobs:
  web-deploy:
    name: 🚀 Deploy Website Every Commit
    runs-on: ubuntu-latest
    steps:
      - name: 🚚 Get Latest Code
        uses: actions/checkout@v3

      - name: 📂 Sync Files
        uses: SamKirkland/web-deploy@v1
        with:
          target-server: staging.learnwebcode.com
          remote-user: youruser
          private-ssh-key: ${{ secrets.SSH_KEY }}
          destination-path: /home/youruser/staging.learnwebcode.com/wp-content/themes/my-awesome-theme/
```

commit changes to staging

## Workflow example

```bash
git checkout -b amazing-feature
# change code
git add .
git commit -m "added amazing-feature"
git push --set-upstream origin amazing-feature
```

Go into Git hub and create a pull request amazing-feature -> staging.

If staging is good. Create a pull request amazing-feature -> main.

## Git approach

```bash
git pull main
git checkout main
git checkout -b feature/final
git add .
git commit -m "final changes"
git checkout staging
git merge feature/final
git push origin staging
git checkout main
git merge feature/final
git push origin main
```
