# Setup Google Cloud Platform

## Create a new Project

Click on "Neues Projekt".
Give the project a name.
Click on "Erstellen"

### Put the public key to GCP

1. Go to "Compute Engine"
2. Go to "metadata"
   If this page cannot be loaded and shows an error then it might help that you go to "VM instances" and activate the "Compute engine API"
3. Click on "Add ssh key"
4. Cat the public key inside your terminal and copy paste it into GCP
5. Click "save"

## Create virtual machine (VM)

### Add a public SSH key

For authentication with your VM via SSH we use SSH keys. [How to create SSH keys is described here](../01_network-protocols/secure-shell.md#Generate-a-SSH-key).

Now copy the public key and go to `GCP -> Compute Engine -> Metadaten` and klick on "SSH-Schlüssel hinzufügen". Insert the key and save.

### Create VM instance

1. Go to "VM-Instanzen"
2. Click on "Instanzen erstellen"
3. [OPTIONAL] Click on "Aktivieren" for "Compute Engine API"
4. Define location and region
5. Define machine type
6. Define operating system and memory

### Connect to VM

After you have created the VM instance in the previous section, it takes some time for GCP to finish the process of creating the VM.

[!NOTE]
You can [connect to your remote server using VSCode](../01_network-protocols/secure-shell.md#How-to-connect-to-a-remote-server-using-VSCode).

Afterwards copy the `external IP` and go to your terminal and use the following command in order to connect to your VM.

```sh
ssh -i <PATH_TO_PRIVATE_SSH_KEY> <VM_USER_NAME>@<EXTERNAL_IP>
```

![NOTE]
Read more about it [here](../01_network-protocols/secure-shell.md#-Authenticate-using-SSH-keys).
You can use a ssh-config file in order to speed up the authentication to your VM.

After connecting to your VM successfully check the number of cores and memory using `htop`.

### Stop the VM inside the VM

```bash
sudo shutdown now

# go to GCP in your browswer and check VM instance. It should be stopped now.
```

## Install packages on Linux

You can find a detail explanation of [how to install packages](../03_linux/install-packages.md#Install-packages) like

- conda
- docker
- docker-compose
- terraform
- pgcli

## Define GCP credentials

We need a way to authorize with Google Cloud in order to interact with GCP (e.g. pulling or pushing data to Google Cloud storage, write queries on BigQuery, ...) from outside the cloud.

For this we can create an service account and we can define different permissions like read and write permission for Google cloud storage and only read permissions for BigQuery.

### Create service account on GCP

Go to "IAM and Verwaltung"
Go to "Dienstkonten"
Go to "3 dots" and click on "Create new service account".

**Define roles**

1. Define a name and [optional] description
2. Define roles:

- Storage admin
- BigQuery admin

3. Skip

[!Note] In a real life project you will / should not asign admin permissions.

**Define further roles**

1. Click on "IAM"
2. Edit the bucket by clicking on the pen next to the bucket
3. Add the permission

### Create Google cloud credentials as json

In order to connect from your local or remote environment to Google Cloud Platform you need to create an authorization token (with the permissions defined in the service account or any other account you want to connect to).

To achieve this you can download those credentials as json file and put it inside your project structure.

1. Go to "Dienstkonten"
2. Click on the "3 dots" of the service account
3. Click on "Schlüssel verwalten"
4. Click on "json"
   -> Key will be downloaded

5. Go to your local development environment
6. Create a .json file where you can copy paste the

## Authenticate with google cloud

1. Option: Hard code in provider google section
2. Option: `gcloud default auth-login`
3. Option: `export GOOGLE_CREDENTIALS=<PATH TO YOUR CREDENTIALS>` # export GOOGLE_CREDENTIALS="$PWD/keys/.my-creds.json"
4. Option: `gcloud auth activate-service-account --key-file $GOOGLE_APPLICATION_CREDENTIALS` with `export GOOGLE_APPLICATION_CREDENTIALS=~/keys/.my-creds.json`

## Configure Google Cloud CLI

```bash
# Define env variable needed for authentication
export GOOGLE_APPLICATION_CREDENTIALS=~/keys/.my-creds.json

# Authenticate with GCP
gcloud auth activate-service-account --key-file $GOOGLE_APPLICATION_CREDENTIALS

# Activated service account credentials for: [yyy@xxx.iam.gserviceaccount.com]
```
