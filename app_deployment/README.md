# METALP Data Portal Deployment

Instruction to deploy the METALP Data Portal on the server.

## Create an user for the app

Create an user which will run the app.
```bash
sudo adduser metalp_portal
```

## Pull the latest version

Once logged in the server, switch to the `metalp_portal` user and go to the home dir.
```bash
sudo -u metalp_portal -H bash -l
cd
```

Get the latest version of the app from GitHub.
```bash
git clone https://github.com/mclement18/SBER-METALP-data-portal.git app
```

Or if upgrading version.
```bash
cd app
git pull
```

## Install Packages and Dependancies

### External dependencies

First install all external dependancies that R packages might rely on.
You will need to do with root access. If you are still logged in the app user, log out with `logout`.

For the METALP Data Portal the following dependencies are needed:
- Node.js (tested version: 14.9.0)
- Terser.js (tested version: 5.3.0)
- Cairo graphic library

#### Node installation

Installation of Node.js v14.x.
```bash
# Add the NodeSource package signing key
curl -sSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo apt-key add -
# Add the desired NodeSource repository
VERSION=node_14.x
DISTRO="$(lsb_release -s -c)"
echo "deb https://deb.nodesource.com/$VERSION $DISTRO main" | sudo tee /etc/apt/sources.list.d/nodesource.list
echo "deb-src https://deb.nodesource.com/$VERSION $DISTRO main" | sudo tee -a /etc/apt/sources.list.d/nodesource.list
# Update package lists and install Node.js
sudo apt-get update
sudo apt-get install nodejs
```

#### Terser installation

Once Node installed, install terser globally with npm.
```bash
sudo npm install terser -g
```

#### Cairo

Install Cairo library.
```bash
sudo apt-get install libcairo2-dev
```

And other dependencies for R `Cairo` package installation.
```bash
sudo apt-get install libgtk2.0-dev xvfb xauth xfonts-base libxt-dev
```

### R Packages

Now install all required R packages using the `packages_installation.R` script in the app directory.

```bash
sudo su - \
-c "R -f path-to-app/packages_installation.R"
```

## Add data

For the app to be functional, send the data to the app `data` folder. If not present, create it.
From your local computer.
In the following exemple, a local data folder is being copied to the app directory on the server.
```bash
rsync -r data metalp_portal@<HOST>:~/app/
```

## Restart the App

To make sure that your changes are taken in account, restart the app.
In the app directory and logged as the app user.
```bash
touch restart.txt
```

## Development apps deployment

Follow the same procedure except that you must use the `shiny` user and install the apps within the `/home/shiny/apps/` directory.