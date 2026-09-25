# Homelab GitOps Infrastructure

Declarative, version-controlled Docker Compose stack definitions managed via Portainer CE on Fedora Server.

This repository serves as the single source of truth for service deployments, prioritizing host-bound data persistence (/srv/) and strict secret isolation.

---

## Architecture Workflow

[ VS Code ] 
       |
       |  git push
       v
[ GitHub Repository ]
       |
       |  webhook / poll
       v
[ Portainer CE ] <--- Injected Secrets (Local .env)
       |
       |  docker.sock
       v
[ Containers on Fedora Server ] 

<img width="2816" height="1536" alt="arch" src="https://github.com/user-attachments/assets/b7ffe95e-1aba-48f0-83ab-821b90fd1824" />

---

## Key Design Principles

### 1. Host-Bound Data Persistence
To eliminate container-internal volume drift and prevent database corruption:
* All persistent configs, application states, and downloads are bound directly to host directories under /srv/<service_name>/.
* Core configuration files and folders must exist on the host before initiating stack deployment to avoid directory creation collisions.

### 2. Secret & Environment Variable Isolation
* Zero Secrets in Git: All passwords, domain names, API keys, and system public keys are kept out of the remote repository.
* .gitignore explicitly ignores .env files across all subdirectories.
* Live operational variables are injected at runtime via Portainer's Advanced Environment Variable UI during stack setup.

### 3. Unified Container Networking
* Services requiring external routing or inter-service communication attach to a shared Docker bridge network (caddy_default).
* Caddy acts as the single entry point, managing reverse proxying and automated TLS certificates.

---

<img width="1920" height="813" alt="portainer" src="https://github.com/user-attachments/assets/b3a47465-3eb0-4a31-90f8-536131dea709" />

