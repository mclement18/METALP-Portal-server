# Shiny Server Installtion on Ubuntu

Steps to install shiny server with R

## R 4.0 installation

First add sources for the correct Ubuntu release.
In this case, the _Bionic Beaver (18.04;LTS)_ release.

```bash
sudo touch /etc/apt/sources.list.d/R-sources.list
echo -e "\n## R sources for Bionic Beaver (18.04;LTS)\ndeb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/" \
| sudo tee -a /etc/apt/sources.list.d/R-sources.list
```

You will probably need to install the public key used to sign the __CRAN__ archives.

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
```

Then install R.

```bash
sudo apt-get update
sudo apt-get install r-base
```

Install dev packages to use `install.packages()` function.

```bash
sudo apt-get install r-base-dev
```


## Shiny installation

You will need to install Shiny package before installing shiny server.

### Newest Version

To install the latest availble version use `install.packages()` function.

```bash
sudo su - \
-c "R -e \"install.packages('shiny', repos='https://cran.rstudio.com/')\""
```

### Older version

If you need an older version of shiny, you can install it with the `devtools` package.

To install `devtools`, ensure that **libcurl**, **openssl** and **libxml-2.0** are installed.

```bash
sudo apt-get install libcurl4-openssl-dev libssl-dev libxml2-dev
```

Then install `devtools` package.

```bash
sudo su - \
-c "R -e \"install.packages('devtools', repos='https://cran.rstudio.com/')\""
```

Now, you can use `devtools::install_version()` to install an older version like `shiny_1.4`.

```bash
sudo su - \
-c "R -e \"devtools::install_version('shiny', version='1.4', repos='https://cran.rstudio.com/')\""
```

## Shiny Server installation

To install Shiny Server, you need to first install `gdebi` which is used to install Shiny Server and all of its dependencies.

```bash
sudo apt-get install gdebi-core
```

Then install Shiny Server

```bash
wget https://download3.rstudio.org/ubuntu-14.04/x86_64/shiny-server-1.5.14.948-amd64.deb
sudo gdebi shiny-server-1.5.14.948-amd64.deb
```
