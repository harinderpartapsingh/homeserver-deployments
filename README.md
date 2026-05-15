# Home Server Deployments

This repository contains the deployment configuration for my self-hosted applications running on a Linux home server. It manages Docker Compose services, Nginx reverse proxy configuration, Cloudflare Tunnel routing, and the deployment workflow used to run multiple applications behind HTTPS.

## Architecture Overview

The system is designed to expose self-hosted applications securely without opening inbound ports directly on the home server. Public traffic first reaches Cloudflare, then is routed through a Cloudflare Tunnel to the `cloudflared` container running on the server. From there, requests are forwarded to an Nginx reverse proxy container, which routes traffic to the correct application container.

<img width="1860" height="770" alt="home_server_architecture_clean_v2 drawio" src="https://github.com/user-attachments/assets/64824477-30af-4e66-9f2a-baac54d615e5" />

## Request Flow

```text
User Browser
   → Cloudflare Edge DNS + SSL + CDN
   → Cloudflare Tunnel
   → cloudflared container
   → Nginx reverse proxy container
   → Application container
