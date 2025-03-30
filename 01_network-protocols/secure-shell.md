# Secure shell

SSH (Secure Shell) is a cryptographic network protocol used to securely access and manage remote systems over an unsecured network by providing encrypted communication and authentication.

## How to connect to a remote server via SSH (terminal)

We assume that there is already a remote server running.

There are multiple ways to get access to the server. But all have in common that you have to authenticate yourself. The two common methods are either via password or SSH key.

SSH keys provide better security, convenience, and protection from various types of attacks compared to traditional passwords.

### Authenticate with username and password

```sh
# connect to the server using:
ssh <VM_USER_NAME>@<EXTERNAL_IP>

# Afterwards you will be asked to enter the password
```

`<VM_USER_NAME>` the username of the remote server that we want to login

`<EXTERNAL_IP>` the hostname or external IP address of the server

### Authenticate using SSH keys

```sh
# connect to the server using:
ssh -i PATH_TO_PUBLIC_SSH_KEY <VM_USER_NAME>@<EXTERNAL_IP>
```

`PATH_TO_PUBLIC_SSH_KEY` Path to your public key (inside your .ssh folder)

### How to connect to a remote server using VSCode

1. Install the extension `Remote - SSH` / `ms-vscode-remote.remote-ssh`
2. Press `F1`
3. Type in: `Remote-SSH: Connect to Host...`
4. Select configured SSH host or enter user@host

Since we have already created a config file for SSH it should suggest your config alias.

5. Select the platform of your remote server (Linux / Windows / MacOs)

## Generate a SSH key

SSH keys are used for secure authentication when accessing remote systems, such as servers or cloud environments, without the need for passwords.

> [!NOTE] > **Authentication: "Who are you?"**
> Authentication is the process of verifying the identity of a user, device, or system. It ensures that the entity requesting access is genuinely who they claim to be.
> Examples: Username and password, ssh keys

> [!NOTE] > **Authorization: "What can you do?"**
> Authorization comes after authentication and defines what an authenticated user or system is allowed to do or access. It determines permissions for different actions and resources within the system.
> Examples: User can be assigned permissions through Role-based Access Control (RBAC), OAuth (token based autorization method)

Follow the instructions on https://cloud.google.com/compute/docs/connect/create-ssh-keys#create_an_ssh_key_pair:

```sh
ssh-keygen -t rsa -f ~/.ssh/<KEY_FILENAME> -C <USERNAME>
```

- `KEY_FILENAME` the file name for the generated ssh keys. The private key will be saved under ~/.ssh/KEY_FILENAME and the public key under ~/.ssh/KEY_FILENAME.pub
- `USERNAME` the user name on the virtual machine

[!NOTE]
You will be asked to define a passphrase. You can but you do not have to.

```sh
# Output
Generating public/private rsa key pair.
Enter passphrase for "/c/Users/.../<KEY_FILENAME>" (empty for no passphrase): ...
Enter same passphrase again: ...
Your identification has been saved in "/c/Users/.../<KEY_FILENAME>"
Your public key has been saved in /c/Users/.../<KEY_FILENAME>.pub
The key fingerprint is:
SHA256: ...
The key's randomart image is:
+---[RSA ...]----+
...
```

The private and the public key are now saved as `<KEY_FILENAME>` and `<KEY_FILENAME>.pub` inside the specified location.

## Use SSH config

You can use an ssh configuration in order to speed up connecting to your remote server.

Create a file called `config` inisde `~/.ssh/` folder:

[!IMPORTANT]
If you already have a `~/.ssh/config` file but you want to add another configuration then put both configs into separated config files like `~/.ssh/config-1/config` and `~/.ssh/config-2/config` and use the `Include` directive inside `~/.ssh/config` in oder to reference a specific config like `Include ~/.ssh/config-2/config`.

Or you specify the `-F` flag like `ssh -F /path/to/your/custom_config username@hostname`.

```bash
Host <ALIAS>
HostName <EXTERNAL_ID>
USER <USER_NAME>
IdentityFile SSH_KEY_FILE_PATH
```

- `ALIAS` name of script
- `EXTERNAL_ID` the external IP address of the VM instance
- `USER_NAME` the user of the VM
- `SSH_KEY_FILE_PATH` the path to your private SSH key (`~/.ssh/...`)

Inside your terminal simply run `ssh <ALIAS>` in order to connect to your remote server.

## Port forwarding

We use port forwarding when you run a service on your remote server like jupyter notebook, pgAdmin or any other docker container that exposes a port and you have to access it on your local machine.

### VScode

1. Open a terminal
2. Go to "PORTS"
3. Write down the port to forward

### Terminal

```sh
ssh -R <remote-port>:<local-host>:<local-port> username@remote-server
```

`remte-port` The port on the remote server to forward.

`local-host` The host on your local machine (often localhost or 127.0.0.1).

`local-port` The port on your local machine that the remote server will forward to.

**Enable Port forwarding on SSH server**

For remote port forwarding to work, you must ensure that the remote SSH server allows remote port forwarding. The server’s SSH configuration file (/etc/ssh/sshd_config) must have the following setting enabled:

```bash
GatewayPorts yes
```

This allows the server to accept remote port forwarding. If it’s not enabled, you can modify the server’s sshd_config file and restart the SSH service.

Edit the /etc/ssh/sshd_config file on the remote server.

Find and update (or add) the line:

```bash
GatewayPorts yes
```

Restart the SSH service on the remote server:

```bash
sudo systemctl restart sshd
```

## File transfer over SSH with SFTP

SFTP (Secure File Transfer Protocol) is a protocol used to securely transfer files between a local machine and a remote server or between two remote servers. SFTP is built on top of SSH, so it uses the same encrypted connection as SSH to ensure that file transfers are secure.

```bash
# Start an SFTP session
sftp username@hostname_or_ip

# Upload files to the remote server
sftp> put local_file.txt

# Dowload files to the local machine
sftp> get remote_file.txt
```
