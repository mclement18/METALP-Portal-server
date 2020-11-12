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
- `libsodium-dev` for the R `sodium` package
- MySQL client to connect to remote DB and install R `RMySQL` package

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

#### Dependencies for Sodium package

Dependency needed for the R `sodium` package.
```bash
sudo apt-get install libsodium-dev
```

#### MySQL client and Workbench

Install MySQL client to connect to a remote DB and install the R `RMySQL` package.
```bash
sudo apt-get install mysql-client libmysqlclient-dev
```


##### (Optional) MySQL Workbench

Install MySQL Workbench to easily interact with the database via GUI.

First configure the MySQL APT repository. In the selection menu you only need to enable the tools and connectors to download Workbench.
```bash
# Get the configuration wizard
wget https://dev.mysql.com/get/mysql-apt-config_0.8.15-1_all.deb
# Run it and enable only the tools and connectors
sudo dpkg -i mysql-apt-config_0.8.15-1_all.deb
# Remove deb file
rm mysql-apt-config_0.8.15-1_all.deb
# Update packages information
sudo apt-get update
```

Install MySQL Workbench and dependencies.
```bash
sudo apt-get install mysql-workbench-community dbus-x11 gnome-keyring
```

### R Packages

Now install all required R packages using the `packages_installation.R` script in the app directory.

```bash
sudo su - \
-c "R -f path-to-app/packages_installation.R"
```

## Add data

For the app to be functional, send the data to the app `data` folder. If not present, create it.

In the following exemple, a local data folder is being copied to the app directory on the server.
```bash
# From your local computer
rsync -r data metalp_portal@<HOST>:~/app/
```

## Compile assets

You need to manually compile the assets using the `assets_compilation.R`.

Do it in the app directory as the app user.
```bash
R -f assets_compilation.R
```

## Create or update secret.R file

The app use several global values that are not tracked in the version control.

You will have to create the `secret.R` in the app directory and it should contain the following variables:
```r
# Environment
ENV <- 'production'

# DB credentials
MY_DB_NAME <- 'db_name'
MY_DB_HOST <- 'host_address'
MY_DB_PORT <- 3306
MY_DB_USER <- 'username'
MY_DB_PWD <- 'password'
```

## Create a .my.cnf file in the app user directory

To use the DB backup functionality of the app, you need to create a `.my.cnf` file in the app user `home` directory.

This file is used to set the username, password, host and port value for the connection to the DB using the ternimal client commands, such as `mysqldump`.

In the `.my.cnf` (replace the `<values>` with your actual settings):
```
[client]
user=<username>
password=<password>
host=<host>
port=<port>
```

## Restart the App

To make sure that your changes are taken in account, restart the app.
```bash
# In the app directory and logged as the app user.
touch restart.txt
```

## Development apps deployment

Follow the same procedure except that you must use the `shiny` user and install the apps within the `/home/shiny/apps/` directory.
