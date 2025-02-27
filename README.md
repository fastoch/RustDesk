# RustDesk

Free and Open-Source Remote Desktop Software

## Resources

- NetworkChuck video: https://www.youtube.com/watch?v=EXL8mMUXs88
- install RustDesk client: https://rustdesk.com/
- install Docker: https://docs.docker.com/engine/install/
- Install your own RustDesk server with Docker: https://rustdesk.com/docs/en/self-host/rustdesk-server-oss/docker/

---

# Intro

- It works on Mac, Windows, and Linux. Even on your phone
- It can be **self-hosted**

## The issue with Microsft Remote Desktop solution

The computer we use to remote into another computer can be Windows Home, but the computer you're remoting into has to run Windows Pro.    

## Why not use TeamViewer or Anydesk?

Because these solutions can't be self-hosted, you need to use their servers. Do you trust them enough for that?   
To be fair, Anydesk offers the possibility to have your own server, but it's a paid option.  

The only free and open-source solution that can be self-hosted is **RustDesk**.  
RustDesk also has paid options, but we don't need them.

---

# RustDesk

## RustDesk client

Experience is similar to Teamviewer or Anydesk but free and open-source (FOSS).

## RustDesk Server (with Docker)

Your server can be installed on a VPS (in the Cloud) or locally (on premises).  

Let's say we go with VPS:
- choose a VPS provider (Linode, Hostinger, Cloudways, Ionos, DigitalOcean)
- set up a new server: a small instance running Ubuntu 24.04 will do

Official RustDesk documentation: https://rustdesk.com/docs/en/self-host/rustdesk-server-oss/install/  
_"You need to have Linux installed, script is tested working with CentOS Linux 7/8, Ubuntu 18/20/22/24, and Debian.  
A server with 1 CPU, 1 GB RAM, and 10 GB disk is plenty to run RustDesk."_

### SSH configuration

So, once we have our VPS set up:
- open a terminal on your local workstation (if it's not a Linux machine, just use WSL2)
- `cd` into your .ssh folder
- generate an SSH key pair: `ssh-keygen -t ed25519`
- display the public key with `cat id_ed25519.pub` 
- Select all of the displayed content, and copy it
- paste the key where needed so it's added to your VPS config
- grab the IP address of your VPS
- open a terminal on your local workstation
- ssh into your VPS: `ssh root@<VPS_IP_address>` (it will check the local SSH private key against the VPS public key)

### Install Docker Engine

- Once logged in to your VPS via SSH, run `sudo apt update` and `sudo apt upgrade`

Before installing Docker engine on a new Ubuntu host, you need to set up the Docker `apt` repository.  
To do that, 

### Install the Rustdesk servers 



@15/22
