# Stay Saga Infrastructure

This repository powers the runtime backbone of **Stay Saga**: a modern accommodation booking platform built with a microservices architecture.

Instead of focusing on API details, this README gives you the big picture: what runs, how services connect, and where to start if you want to deploy or contribute.

## Why this project is interesting

Stay Saga is designed as a production-minded, end-to-end platform with:

- A decoupled service architecture for clear ownership and scalability
- Secure identity and access management with Keycloak
- Event-driven communication support through Kafka
- Centralized ingress/routing and TLS termination via Traefik
- Containerized deployment for repeatable environments

## Architecture at a glance

The Docker-based setup currently orchestrates:

- `client` (Next.js frontend)
- `gateway` (API Gateway / reverse proxy layer)
- `user`, `property`, `order`, `payment` services
- `keycloak` for authentication and authorization
- `postgres` for service data and identity data
- `kafka` for asynchronous/event-driven workflows
- `traefik` as the public edge router with HTTPS

## Deployment entrypoint

For full Docker deployment instructions, environment setup, and operational commands, see:

- [docker/README.md](docker/README.md)
- [docker/docker-compose.yml](docker/docker-compose.yml)

## Current documentation scope

API endpoints are intentionally not documented in depth yet.  
This infrastructure documentation currently prioritizes platform topology, deployment flow, and service relationships.

## Stay Saga repositories

- Infrastructure: <https://github.com/tienhai2808/stay_saga-infra>
- Frontend Client: <https://github.com/tienhai2808/stay_saga-client>
- API Gateway: <https://github.com/tienhai2808/stay_saga-gateway>
- Shared Common Library: <https://github.com/tienhai2808/stay_saga-common>
- gRPC Contracts: <https://github.com/tienhai2808/stay_saga-grpc>
- User Service: <https://github.com/tienhai2808/stay_saga-user>
- Property Service: <https://github.com/tienhai2808/stay_saga-property>
- Order Service: <https://github.com/tienhai2808/stay_saga-order>
- Payment Service: <https://github.com/tienhai2808/stay_saga-payment>

## Roadmap direction

Near-term infrastructure improvements include:

- Kubernetes-oriented deployment assets
- Better environment promotion strategy (dev/staging/prod)
- Observability hardening (metrics, traces, and alerting)
- CI/CD automation for multi-repo delivery
