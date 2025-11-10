# PPRD Systems: Security Infrastructure

> **Building a Secure Foundation for Modern Software Systems**  
> *PPRD Systems: Innovating across IoT, Fintech, and Healthcare domains.*

This repository provides a **ready-to-deploy local security infrastructure** designed for **development and testing environments**.  
It currently integrates **Keycloak** for authentication and authorization, backed by **PostgreSQL**, and will later include **OpenFGA** for fine-grained access control.

> Designed for **local deployment** during the initial development and testing phases, but it can easily evolve into a **production and cloud deployments** suitable for microservices, IoT platforms, Healthcare systems, event-driven architectures, and others.


> # ⚠️ Disclaimer
> This repository is part of an ongoing **research and experimental development projects** conducted by **PPRD Systems, LLC** for educational and exploratory purposes only.
>  
> ⚖️ **Liability & Usage**
Use of this repository or its components is **strictly at the user’s own risk**.
**PPRD Systems, LLC** provides all materials **“as is,” without any warranties**, express or implied.
> 
> © **2025 PPRD Systems, LLC**. All rights reserved

## Overview

This stack is part of **PPRD Systems' modular infrastructure platform**, enabling secure identity and access management for distributed systems.

It includes:
- **Keycloak Authentication Server** — Centralized identity provider supporting OpenID Connect, OAuth2, and SAML.
- **PostgreSQL Database** — Backend for Keycloak’s persistent data store.
- **OpenFGA** — Fine-grained authorization service for complex access policies. *(Upcoming)* 

Use this setup to prototype and test secure authentication flows, RBAC/ABAC models, and token-based security.

## Stack Components

| Service | Description | Default Ports |
|----------|--------------|----------------|
| **Keycloak** | Identity and Access Management solution supporting OpenID Connect, OAuth 2.0, and SAML 2.0. | `7080 (HTTP)`, `7443 (HTTPS)` |
| **PostgreSQL** | Relational database used by Keycloak to persist user and configuration data. | `5432` |
| **OpenFGA** *(Upcoming)* | Fine-Grained Authorization service for policy-based access control. | TBD |

## Docker Compose Setup

This repository includes a `docker-compose.yml` that orchestrates all services:

```yaml
services:
  keycloak-auth-server:
    image: quay.io/keycloak/keycloak:24.0
    container_name: keycloak-auth-server
    environment:
      KC_HOSTNAME: localhost
      KC_HOSTNAME_PORT: 7080
      KC_HOSTNAME_STRICT_BACKCHANNEL: "true"
      KEYCLOAK_ADMIN: ${KEYCLOAK_ADMIN_USERNAME:-}
      KEYCLOAK_ADMIN_PASSWORD: ${KEYCLOAK_ADMIN_PASSWORD:-}
      KC_HEALTH_ENABLED: "true"
      KC_LOG_LEVEL: info

      KC_DB: postgres
      KC_DB_URL: jdbc:postgresql://keycloak-auth-server-db:5432/keycloak
      KC_DB_USERNAME: ${KEYCLOAK_DB_USERNAME:-}
      KC_DB_PASSWORD: ${KEYCLOAK_DB_PASSWORD:-}
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:7080/health/ready"]
      interval: 15s
      timeout: 2s
      retries: 15
    depends_on:
      - keycloak-auth-server-db
    command: ["start-dev", "--http-port", "7080", "--https-port", "7443"]
    ports:
      - "7080:7080"
      - "7443:7443"
    networks:
      - local_network

  keycloak-auth-server-db:
    image: postgres:13
    container_name: keycloak-auth-server-db
    environment:
      POSTGRES_DB: ${POSTGRES_DB:-}
      POSTGRES_USER: ${POSTGRES_USER:-}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-}
    volumes:
      - keycloak_db_data:/var/lib/postgresql/data
    networks:
      - local_network

networks:
  local_network:
    driver: bridge
volumes:
  keycloak_db_data:
```

## Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/pprds-public/security-infrastructure.git
cd security-infrastructure
```

### 2. Configure Environment Variables

### 3. Start the Security Stack
```bash
docker compose up -d
```

### 3. Access the Keycloak Admin Console
- **URL:** [http://localhost:7080](http://localhost:7080)  
  **Username:** `KEYCLOAK_ADMIN_USERNAME`  
  **Password:** `KEYCLOAK_ADMIN_PASSWORD`

## Directory Structure

```
security-infrastructure/
├── keycloak/
├── openfga/
├── docker-compose.yml
└── README.md
```

## Future Plans

- Integrate **OpenFGA** for fine-grained authorization.
- Add **Kubernetes manifests** or **Helm charts** for cloud deployment.
- Implement **Grafana Mimir/Loki hooks** for security auditing and monitoring.
- Extend **Keycloak realm provisioning** with environment templates.

## About PPRD Systems

PPRD Systems is an engineering startup specializing in **IoT, Healthcare, and Fintech software solutions**, combining **real-time data pipelines**, **AI integration**, and **cloud-native architecture**.

We focus on:
- Secure and scalable microservice design.
- Predictive analytics and intelligent automation.
- Observability and security infrastructure for data-driven systems.

**Website:** Coming Soon
**LinkedIn:** [PPRD Systems](https://www.linkedin.com/company/pprdsystems)  
**Contact:** contact@pprdsystems.com

## License
This project is licensed under the [Apache License 2.0](LICENSE).


> # ⚠️ Disclaimer
> This repository is part of an ongoing **research and experimental development projects** conducted by **PPRD Systems, LLC** for educational and exploratory purposes only.
>  
> ⚖️ **Liability & Usage**
Use of this repository or its components is **at your own risk**. **PPRD Systems, LLC** provides all materials **“as is,” without any warranties**, express or implied.
> 
> © **2025 PPRD Systems, LLC**. All rights reserved