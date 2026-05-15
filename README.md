# Home Server Deployments

This repository contains the deployment configuration for my self-hosted applications running on a Linux home server. It manages Docker Compose services, Nginx reverse proxy configuration, Cloudflare Tunnel routing, and the CI/CD workflow used to deploy multiple applications behind HTTPS.

The goal of this setup is to keep application deployment automated, repeatable, and secure while avoiding direct exposure of the home server to the public internet.

## Architecture Overview

Public traffic first reaches Cloudflare, where DNS, SSL, and edge routing are handled. Cloudflare then routes traffic through a Cloudflare Tunnel to the `cloudflared` container running on the home server.

Inside the home server, requests are forwarded to an Nginx reverse proxy container. Nginx routes each request to the correct application container based on the domain or route configuration.

<img width="1860" height="770" alt="home_server_architecture_clean_v2 drawio" src="https://github.com/user-attachments/assets/64824477-30af-4e66-9f2a-baac54d615e5" />

## Request Flow

User traffic follows this path:

<pre>
User Browser
   → Cloudflare Edge DNS + SSL + CDN
   → Cloudflare Tunnel
   → cloudflared container
   → Nginx reverse proxy container
   → Application container
</pre>

## CI/CD Deployment Flow

Application source code lives in separate repositories. When code is pushed to an application repository, GitHub Actions builds a Docker image and publishes it to GitHub Container Registry.

This deployment repository stores the Docker Compose and infrastructure configuration used by the home server. When the deployment configuration is updated, a self-hosted GitHub Actions runner on the home server pulls the latest Docker images and applies the updated Docker Compose configuration.

<pre>
App Repository
   → GitHub Actions
   → GitHub Container Registry
   → Deployment Repository
   → Self-Hosted Runner
   → Docker Compose
   → Updated containers on home server
</pre>

## Deployed Applications

This deployment setup is used to host multiple self-managed applications behind a shared Nginx reverse proxy.

| Application | Purpose |
|---|---|
| Portfolio App | Personal developer portfolio |
| ParcelEnRoute | Parcel delivery marketplace application |
| HomeEatz | Home-based food marketplace application |

## Repository Structure

<pre>
.
├── .github/
│   └── workflows/         # GitHub Actions workflows for deployment
├── infra/
│   └── nginx/             # Nginx reverse proxy configuration
├── docker-compose.yml     # Docker Compose services for apps, proxy, and tunnel
└── README.md              # Architecture and deployment documentation
</pre>

## Key Components

| Component | Purpose |
|---|---|
| Cloudflare Edge | Handles DNS, SSL, CDN, and public HTTPS traffic |
| Cloudflare Tunnel | Securely connects Cloudflare to the home server without exposing inbound ports |
| cloudflared container | Runs the Cloudflare Tunnel client inside Docker |
| Nginx reverse proxy | Routes incoming requests to the correct application container |
| Docker Compose | Manages application, proxy, and tunnel containers |
| GitHub Actions | Builds Docker images and triggers deployment updates |
| GitHub Container Registry | Stores versioned Docker images |
| Self-hosted runner | Runs deployment commands directly on the home server |
| Deployment repository | Stores Docker Compose, Nginx, and deployment configuration |

## Why This Architecture

This architecture separates application source code from deployment configuration. Each application can be built and versioned independently, while this repository controls how those images are deployed on the home server.

The setup focuses on:

- Secure public access through Cloudflare Tunnel
- Automated Docker-based deployments
- Centralized Nginx reverse proxy routing
- Reusable deployment configuration
- Separation between application repositories and infrastructure configuration
- Self-hosted CI/CD using a GitHub Actions runner

## Deployment Summary

1. Code is pushed to an application repository.
2. GitHub Actions builds the Docker image.
3. The image is pushed to GitHub Container Registry.
4. The deployment configuration is updated.
5. The self-hosted runner on the home server is triggered.
6. Docker Compose pulls the latest image.
7. Containers are recreated or updated.
8. Traffic is routed through Cloudflare Tunnel and Nginx.

## Technologies Used

- Docker
- Docker Compose
- Nginx
- Cloudflare Tunnel
- GitHub Actions
- GitHub Container Registry
- Self-hosted GitHub Actions Runner
- Linux
