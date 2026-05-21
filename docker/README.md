# Stay Saga Docker (Production)

Single-file deployment only: all routing is defined directly in `docker-compose.yml` (no extra Traefik dynamic config files).

## Flow deploy

1. Copy env template:
   - `cp .env.example .env`
2. Edit `.env` (domain, db password, keycloak secrets, payos keys).
3. Start infra + Keycloak first:
   - `docker compose up -d traefik postgres keycloak`
   - if you changed compose labels/config, force recreate:
   - `docker compose up -d --force-recreate traefik keycloak`
4. Open Keycloak at:
   - `https://${KEYCLOAK_DOMAIN}`
5. Manually configure Keycloak realm/clients (details below).
6. Start full stack:
   - `docker compose up -d`

## Keycloak manual config (required)

Use the values from `.env`:

- Realm:
  - `KEYCLOAK_REALM`
- API client for login/password flow:
  - client id: `KEYCLOAK_API_CLIENT_ID`
  - client secret: `KEYCLOAK_API_CLIENT_SECRET`
  - Access type: confidential
  - Enable Direct Access Grants
- Admin client for user-service calling admin API:
  - client id: `KEYCLOAK_ADMIN_CLIENT_ID`
  - client secret: `KEYCLOAK_ADMIN_CLIENT_SECRET`
  - Access type: confidential
  - Enable Service Accounts
  - Service account roles (realm-management):
    - `manage-users`
    - `view-users`
    - `query-users`
- Realm roles:
  - `user`
  - `admin`
  - (optional) add `user` to default roles so register user gets role automatically

## Database migration behavior

Migrations are not run by dedicated `db-migrate-*` jobs in compose.

Current behavior:

- `db-init` creates missing databases (`keycloak`, `user`, `property`, `order`, `payment`).
- Each .NET service runs `db.Database.Migrate()` automatically on startup in `Production`.

## Endpoints

- Frontend: `https://${PUBLIC_DOMAIN}`
- API Gateway: `https://${PUBLIC_DOMAIN}${SV_API_PREFIX}`
- Keycloak: `https://${KEYCLOAK_DOMAIN}`

## Useful commands

- Start all: `docker compose up -d`
- Stop all: `docker compose down`
- Re-run only db init:
  - `docker compose up --force-recreate db-init`
- Restart app services (to trigger migrate-on-start in production):
  - `docker compose restart user property order payment`
- Quick check Keycloak route through Traefik:
  - `curl -kI --resolve ${KEYCLOAK_DOMAIN}:443:127.0.0.1 https://${KEYCLOAK_DOMAIN}/admin/master/console/`

## Notes

- Keep `.env` out of git.
- Ensure DNS `A/AAAA` for `PUBLIC_DOMAIN` and `KEYCLOAK_DOMAIN` points to this host.
- Open firewall inbound `80` and `443`.
