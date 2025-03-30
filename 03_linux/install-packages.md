# Install packages

## Install anaconda

```bash
# download anaconda with link from anaconda download page
wget https://repo.anaconda.com/archive/Anaconda3-2024.10-1-Linux-x86_64.sh

# install anaconda
bash Anaconda3-2024.10-1-Linux-x86_64.sh

# By default anaconda update your shell profile to automatically initialize conda.
# This will activate conda on startup and change the command prompt when activated.

# [Optional]
# If you'd prefer that conda's base environment not be activated on startup,
# run the following command when conda is activated:
conda config --set auto_activate_base false

# [Optional]
# You can undo this by running
conda init --reverse $SHELL

# For changes to take effect, close and re-open your current shell.
# or run
source .bashrc
```

## Install docker

```bash
sudo apt-get update

sudo apt-get install docker.io
```

### Run docker without sudo

```bash
# see: https://github.com/sindresorhus/guides/blob/main/docker-without-sudo.md

# Add the docker group if it doesn't already exist
sudo groupadd docker

# Add the connected user $USER to the docker group
sudo gpasswd -a $USER docker

# IMPORTANT: Log out and log back in so that your group membership is re-evaluated.

# Restart the docker daemon
sudo service docker restart
```

## Install docker-compose

> [!TIP]
> Before installing docker-compose create a `~/bin` directory where you put in your executable files.

```bash
# Get download path from github
# https://github.com/docker/compose/releases/download/v2.33.1/docker-compose-linux-x86_64

# Download docker-compose with specifiying the output (-O)
wget https://github.com/docker/compose/releases/download/v2.33.1/docker-compose-linux-x86_64 -O ~/bin/docker-compose

# you now have ~/bin/docker-compose
# but your system does not know that it is an executable file
chmod +x bin/docker-compose

# Add the bin directory with docker-compose to PATH
# open ~/.bashrc file and add
export PATH="${HOME}/bin:${PATH}"

# restart your VM or run
source ~/.bashrc
```

## Install pgcli with conda

```bash
# install pgcli
conda install -c conda-forge pgcli

# connect to postgres db
pgcli -h localhost -U root -d ny_taxi
```

## Install terraform

```bash
# copy download link for AMD64
https://releases.hashicorp.com/terraform/1.11.1/terraform_1.11.1_linux_amd64.zip

# download
wget https://releases.hashicorp.com/terraform/1.11.1/terraform_1.11.1_linux_amd64.zip

# install unzip
sudo apt-get install unzip

# unzip and remove zip file
unzip terraform_1.11.1_linux_amd64.zip
rm terraform_1.11.1_linux_amd64.zip

# terraform is already a executable
# and ~/bin is already inside PATH
terraform version
```
