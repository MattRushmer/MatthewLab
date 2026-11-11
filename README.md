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
<img width="1911" height="942" alt="image" src="https://github.com/user-attachments/assets/ca9e7f41-870f-4571-baed-6f83292fec42" />
Ive now configured a reverse proxy for Portainer and Authentik. 

I now want to set up Terraform so I can do IAC. I want to set it up so i can edit and setup services with code.
This is my project layout:
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.6"
    }
    authentik = {
      source  = "goauthentik/authentik"
      version = "~> 2026.8.0"
    }
  }
}
EOF

cat > variables.tf <<'EOF'
variable "server_ip" {
  type    = string
  default = "192.168.1.50"
}

variable "authentik_token" {
  type      = string
  sensitive = true
  default   = ""
}
EOF

cat > providers.tf <<'EOF'
provider "docker" {
  host = "unix:///var/run/docker.sock"
}
EOF

terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}

variable "name" {
  type = string
}

variable "image" {
  type = string
}

variable "port" {
  type = number
}

variable "host" {
  type = string
}

variable "network" {
  type = string
}

variable "env" {
  type    = map(string)
  default = {}
}

variable "volumes" {
  type    = map(string)
  default = {}
}

locals {
  labels = {
    "traefik.enable"                                             = "true"
    "traefik.http.routers.${var.name}.rule"                      = "Host(`${var.host}`)"
    "traefik.http.routers.${var.name}.entrypoints"               = "web"
    "traefik.http.services.${var.name}.loadbalancer.server.port" = tostring(var.port)
  }
}

resource "docker_image" "this" {
  name = var.image
}

resource "docker_volume" "this" {
  for_each = var.volumes
  name     = "${var.name}_${each.key}"
}

resource "docker_container" "this" {
  name    = var.name
  image   = docker_image.this.image_id
  restart = "unless-stopped"
  env     = [for k, v in var.env : "${k}=${v}"]

  networks_advanced {
    name = var.network
  }

  dynamic "labels" {
    for_each = local.labels
    content {
      label = labels.key
      value = labels.value
    }
  }

  dynamic "volumes" {
    for_each = var.volumes
    content {
      volume_name    = docker_volume.this[volumes.key].name
      container_path = volumes.value
    }
  }
}
EOF



data "docker_network" "proxy" {
  name = "proxy"
}

module "whoami" {
  source  = "./modules/web-service"
  name    = "whoami"
  image   = "traefik/whoami:latest"
  port    = 80
  host    = "whoami.${var.server_ip}.nip.io"
  network = data.docker_network.proxy.name
}
EOF


Set Up 
<img width="641" height="923" alt="image" src="https://github.com/user-attachments/assets/02d2a257-cfa8-4050-bd86-97f4663358c1" />

I now Want to finally install Netbox. This will be my network Manager then I want to configure my athentik as well as configure the CI/CD
<img width="1914" height="974" alt="image" src="https://github.com/user-attachments/assets/b81999a4-00e7-4822-b722-1a627003f890" />
<img width="1913" height="868" alt="image" src="https://github.com/user-attachments/assets/992719bf-99c4-422d-8253-2c34eb5bad85" />
<img width="1915" height="951" alt="image" src="https://github.com/user-attachments/assets/b02b8473-7ba1-4fc3-9ba0-7729bf986859" />

I've now set um my netbox so I can view and organise my Home lab. currently there is still lots of information to add to this but as my home lab grows i want to add more to it

For this i have decide to use Github Actions for my CI and Argo for my CD. I was goiong to use Jemkins for my Ci but was a bit affraid of it since it seemed alot harder to use so currently im going to stick with GitHub actions. 
I also want to install Prometheus and Grafana For monitoring so i can make sure all my containers are working as intended

I first started by setting p Netbox, authentik and traefik so i can access there statistics
<img width="952" height="956" alt="image" src="https://github.com/user-attachments/assets/f440412e-b67c-4cb8-b1f6-f998977f9fdc" />
<img width="1911" height="930" alt="image" src="https://github.com/user-attachments/assets/98f1da93-42c5-411a-8318-7263bbdecd38" />
<img width="1918" height="937" alt="image" src="https://github.com/user-attachments/assets/3730a068-134f-433a-85f2-7b30db949254" />

No i want to deploy ELK stack. Once i have all these monitoring and admin service running i want to use github actions and Argo inorder to set up my Pipeline. For this ill be using terraform which i installed earlier.
<img width="1108" height="530" alt="image" src="https://github.com/user-attachments/assets/0c2f5c5e-19d0-41c3-8187-63bbcc9f4acf" />
<img width="1434" height="827" alt="image" src="https://github.com/user-attachments/assets/84b48ed9-0cc2-46b2-b875-cd563e83fd47" />

Had an issue where i didnt configure my storage right so the disk i was installing on was only 16gb ion size. had to change the colume size now i have 250g.
<img width="738" height="404" alt="image" src="https://github.com/user-attachments/assets/ae35f8b5-691b-49fd-b161-71148748515b" />
My cpu was to old for the version of file beat i was using so i had to revert to an older version



