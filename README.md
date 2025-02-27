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
Microsoft native RDP software is fairly limited, it doesn't allow to remote access a computer from anywhere, only from the same network.  

## Why not use TeamViewer or Anydesk?

Because these solutions can't be self-hosted, you need to use their servers. Do you trust them enough for that?   
To be fair, Anydesk offers the possibility to have your own server, but it's a paid option.  

The only free and open-source solution that can be self-hosted is **RustDesk**.  
RustDesk also has paid options, but we don't need them.

---

# RustDesk

## The RustDesk client

### Local access (no server)

For machines that are on the same network, they can be accessed via their IP address.  
To configure that:
- install the Rustdesk client on every machine
- start the client and click on the 3 dots next to your machine's Rustdesk ID
- click on Security
  - unlock security settings > enable direct IP access > port 21118 (default value)
  - set a permanent password


@9/22
