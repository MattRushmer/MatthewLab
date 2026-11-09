<img width="4032" height="3024" alt="IMG_0737" src="https://github.com/user-attachments/assets/dd159bd7-0fc6-44c0-9134-4640f3cb3878" /># 🧪 MatthewLab

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
I Started Off by Install my OS and giving it a static Ip of 192.168.1.50
<img width="1917" height="935" alt="Screenshot 2026-11-07 125329" src="https://github.com/user-attachments/assets/542f2555-baf6-45aa-ac12-718b9f05662f" />
<img width="4032" height="3024" alt="IMG_0737" src="https://github.com/user-attachments/assets/d6eef037-5544-4896-88e6-5510000ee7d7" />

I then set up my SSH key so i didn't have to login every time to ssh in.
<img width="1889" height="990" alt="Screenshot 2026-11-07 162218" src="https://github.com/user-attachments/assets/156b7833-1c95-4dd4-9fb6-b69d3abc3054" />

I then installed docker and Portainer in order to monitor my docker containers and networks. Honestly one of the most useful services i have ever used.
<img width="1919" height="945" alt="Screenshot 2026-11-07 162741" src="https://github.com/user-attachments/assets/c41f1994-c4bc-46e4-88aa-093d807879cd" />

I then Set Up Authentik to act as my single sign in system.
<img width="1916" height="936" alt="image" src="https://github.com/user-attachments/assets/4425aba2-6ec2-4cf2-8d36-d37fe1694775" />

Had an issue when trying to setup my Authentik where it wasn't accepting my setup token. By passed by making it read the setup token straight from files and remove it fron the log in process.

I now wanted to set up my reverse proxy manager so i can have nice clean URLs with websites which where safe. 
<img width="1918" height="995" alt="image" src="https://github.com/user-attachments/assets/48ca065b-26ac-45fe-a71b-eba0dcd0e5fc" />
