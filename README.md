# RustDesk

Free and Open-Source Remote Desktop Software

## Resources

- NetworkChuck video: https://www.youtube.com/watch?v=EXL8mMUXs88
- install RustDesk client: https://rustdesk.com/
- install Docker on your private RustDesk server: https://docs.docker.com/engine/install/
- Set up your own RustDesk server with Docker: https://rustdesk.com/docs/en/self-host/rustdesk-server-oss/docker/

---

# Intro

- It works on Mac, Windows, and Linux. Even on your phone
- It can be **self-hosted**

## The issue with Microsoft Remote Desktop solution

The PC we use to remote into another PC can be Windows Home, but the computer you're remoting into has to run Windows Pro.   
Which means you need all your machines to run Windows Pro, which can be expensive.

## Why not use TeamViewer or Anydesk?

Because these solutions can't be self-hosted, you need to use their servers. Do you trust them enough for that?   
To be fair, Anydesk offers the possibility to have your own server, but it's a paid option.  

The only free and open-source solution that can be self-hosted is **RustDesk**.  
RustDesk also has paid plans, but we really don't need them, it's only there to show support.

---

# RustDesk

## RustDesk client

Experience is similar to Teamviewer or Anydesk but free and open-source (FOSS).

## RustDesk Server (with Docker)

Of course, RustDesk, by default, will use its own public servers.  
But you can set up and use your own private server, which can be installed on a VPS (in the Cloud) or locally (on premises).  

Let's say we go with VPS:
- choose a VPS provider (Linode, Hostinger, Cloudways, Ionos, DigitalOcean)
- set up a new server: a small instance running Ubuntu 24.04 will do

Official RustDesk documentation: https://rustdesk.com/docs/en/self-host/rustdesk-server-oss/install/  
_"You need to have Linux installed, script is tested working with CentOS Linux 7/8, Ubuntu 18/20/22/24, and Debian.  
A server with 1 CPU, 1 GB RAM, and 10 GB disk is plenty to run RustDesk."_

### SSH configuration

**Preamble**:  
Many VPS providers initially configure servers to allow both password and key-based authentication.  
If the VPS is configured to allow password authentication, you can SSH into it using a username and password.  
For enhanced security, it's recommended to disable password authentication after setting up key-based authentication.  
This can be done by:
- editing the `/etc/ssh/sshd_config` file and setting: `PasswordAuthentication no`
- Remember to restart the SSH service after making changes to the configuration

To set up key-based authentication:
- open a terminal on your local workstation
  - if it's not Linux, just enable Windows Subsystem for Linux
  - if you use MacOS, open the closest window and throw your laptop as far as you can (I'm being serious)
- `cd` into your .ssh folder
- generate an SSH key pair: `ssh-keygen -t ed25519`
  - add a passphrase for more security (you'll need to enter it each time you'll log in to your VPS)
- display the public key with `cat id_ed25519.pub` 
- Select all of the displayed content, and copy it
- Add the public key to the VPS's `~/.ssh/authorized_keys` file.
- Ensure proper permissions are set on the `.ssh` directory and the `authorized_keys` file
- grab the IP address of your VPS
- open a terminal on your local workstation
- ssh into your VPS: `ssh root@<VPS_IP_address>` (it will check the local SSH private key against the VPS public key)

### Installing the Docker Engine

- Once logged in to your VPS via SSH, run `sudo apt update` and `sudo apt upgrade`

Before installing Docker engine on a new Ubuntu host, you need to set up the Docker `apt` repository.  
Afterward, you can install and update Docker from the repository.  

Official documentation - https://docs.docker.com/engine/install/ubuntu/  

First, install these 2 required packages:
```bash
sudo apt-get install ca-certificates curl
```
- ca-certificates: Allows SSL-based applications to check for the authenticity of SSL connections.
- curl: A tool for transferring data using various protocols

Then, create a directory to store GPG keys for package repositories:
```bash
sudo install -m 0755 -d /etc/apt/keyrings
```
This command creates a directory named "keyrings" in /etc/apt with permissions 0755 (rwxr-xr-x).  
- The leading '0' indicates that **no special modes** (setuid, setgid, or sticky bit) are being set.
- The '755' part sets the standard Unix permissions:
  - '7' (rwx) for the owner
  - '5' (r-x) for the group
  - '5' (r-x) for others

Now, we need to download Docker's GPG Key:
```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```
This will download Docker's GPG key from the specified URL and save it as "docker.asc" in the keyrings directory.  
The GPG key is used to verify the authenticity of Docker packages.  

We must set Permissions for the GPG Key:
```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
This changes the permissions of the downloaded GPG key file to make it readable by all users.  
This ensures that the Ubuntu's package manager can access and use the key when verifying Docker packages.  

Next step is to add the Docker repo to Apt sources.  
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
- The backslashes at the end of each line allow the command to span multiple lines for improved readability.
    - Without these backslashes, the shell would interpret each line as a separate command
- Backslashes are also used to escape the double quotes.
    - This ensures that the inner quotes are treated as literal characters and not as string delimiters
- `echo` outputs the repository information:
  - `deb` indicates that the repository contains pre-compiled binary packages for Debian-based systems, such as Ubuntu.
    - It's part of the APT (Advanced Package Tool) repository format used by Debian and its derivatives.
  - `[arch=$(dpkg --print-architecture)]` specifies the system architecture.
  - `signed-by=/etc/apt/keyrings/docker.asc` indicates the GPG key file for verification.
  - `https://download.docker.com/linux/ubuntu` is the repository URL.
  - `$(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")` determines the Ubuntu release codename.
  - `stable` specifies the repository branch.
  - `sudo tee /etc/apt/sources.list.d/docker.list > /dev/null` writes the output to the Docker repository file.
- `sudo apt-get update` updates the package list on your system, including the newly added Docker repository

Now that we have downloaded Docker's GPG key and added the Docker repo to our package manager sources, we are ready to install
the Docker engine. To install the latest version, run:
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
You can verify that the installation is successful by running `docker version`. 

### Setting up the Rustdesk servers 

There are technically 2 servers:
- **hbbs**: RustDesk ID (rendezvous/signaling) server 
- **hbbr**: Rustdesk relay server

When RustDesk is running on a machine, the machine constantly pings the ID server to make its IP address and port known.  
When you start a connection from computer A to computer B, A contacts the ID server and requests to communicate with B.  
The ID server then attempts to connect A and B directly directly to each other using **hole punching**.  
If hole punching fails, A will communicate with B via the relay server.  

In the majority of cases, hole punching is successful, and the relay server is never used.

To set these servers up on our VPS, we're going to use `docker-compose`.  
For that, we need a `docker-compose.yml` file such as the following:
```yml
services:
  hbbs:
    container_name: hbbs
    image: rustdesk/rustdesk-server:latest
    command: hbbs
    volumes:
      - ./data:/root
    network_mode: "host"

    depends_on:
      - hbbr
    restart: unless-stopped

  hbbr:
    container_name: hbbr
    image: rustdesk/rustdesk-server:latest
    command: hbbr
    volumes:
      - ./data:/root
    network_mode: "host"
    restart: unless-stopped
```

- Log into your VPS via SSH, and create a dedicated directory: `mkdir RustDeskDocker`
- Jump into that folder via `cd RustDeskDocker`
- create and edit your docker-compose file: `vim docker-compose.yml`
- enter insert mode by pressing the i key
- copy-paste the above contents
- press Esc and type :wq to save and quit Vim

Then, to bring our servers up and running, just run this command:
```bash
docker compose up -d 
```
This creates and starts all services defined in your docker-compose.yaml file in detached mode.  

Now, if we run `ls` while being in the same directory, we'll see a folder called `data`, which was created by the previous command execution.   
Let's run `cd data` to jump in that folder and `ls` once more.  
In there, we should find the public SSH key that will allow us to log in to our newly created Docker container.  
- run `cat id_ed25519.pub` to display the public key
- we also need our VPS' IP address, that we can get via `ip add | grep eth0`
- go back to your RustDesk client
- click on the 3 vertical dots next to the ID field
- click on the Network tab
- unlock network settings
- click on ID/Relay server
- in the ID server and Relay server fields, paste in your VPS' IP address
- in the Key field, paste in the public Docker SSH key (not to be confused with the key we use to log in to the VPS)
  - to avoid confusion, you can add a comment to your ssh keys
- click OK and you'll get a 'Successful' message once your server has been added to the client
- of course, this operation needs to be repeated on every RustDesk client

