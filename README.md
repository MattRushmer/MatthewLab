# 🧪 MatthewLab

A self-hosted homelab, rebuilt from scratch with a clear purpose.

## 📖 Background

My homelab started life on an old laptop that I converted into a server. I experimented with a lot of different services, but I never had a clear idea of what I wanted the lab to be or how it should work. Over time, things broke or didn't behave the way I intended.

This project is my fresh start: a deliberate, well-planned rebuild so everything works the way I want it to.

## 🧱 The Stack

| Layer | Choice | Why |
|---|---|---|
| **Operating System** | Fedora Server 45 | Tried Proxmox, Ubuntu and a few others, but Fedora won out. Its short release cycle makes it fun to run the latest features. |
| **Container Runtime** | Docker | Running a single node meant choosing between Podman and Docker. Docker won thanks to its long history, extensive documentation and Docker Compose support. |
| **Single Sign-On** | Authentik | One login for everything. Sign in once and get access to all my services. |
| **Reverse Proxy** | Traefik | Alternatives like Nginx Proxy Manager gave me security and usability concerns. |
| **Container Management** | Portainer | The container management UI that worked best for me. |
| **VPN** | Tailscale | The tunnel system I will use to connect to my home lab when i am outside of my network. |

## 🎯 Goals

- Build a lab that is stable, secure and easy to maintain
- Keep every service behind a single sign-on
- Run everything as code with Docker Compose

## Setup
