# Stay Saga Docker (Production)

## Flow deploy

1. Copy env template:
   - `cp .env.example .env`
2. Edit `.env` (domain, db password, keycloak secrets, payos keys, github token).
3. Start infra + Keycloak first:
   - `docker compose up -d traefik postgres keycloak`
4. Open Keycloak at:
   - `https://${PUBLIC_DOMAIN}${KEYCLOAK_PATH_PREFIX}`
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

## Database migration via Docker Compose (`dotnet ef`)

Migrations are run by one-shot jobs in compose using `dotnet ef database update`:

- `db-init`: create missing databases
- `db-migrate-user`
- `db-migrate-property`
- `db-migrate-order`
- `db-migrate-payment`

Important:

- `db-migrate-*` services mount source code from `../../services/*`.
- You must have source folders available on host (same repo layout).
- `GITHUB_TOKEN` in `.env` must have permission to read GitHub NuGet packages (`read:packages`).

## Endpoints

- Frontend: `https://${PUBLIC_DOMAIN}`
- API Gateway: `https://${PUBLIC_DOMAIN}${SV_API_PREFIX}`
- Keycloak: `https://${PUBLIC_DOMAIN}${KEYCLOAK_PATH_PREFIX}`

## Useful commands

- Start all: `docker compose up -d`
- Stop all: `docker compose down`
- Re-run migrations only:
  - `docker compose up --force-recreate db-migrate-user db-migrate-property db-migrate-order db-migrate-payment`
- Re-run only db init:
  - `docker compose up --force-recreate db-init`

## Notes

- Keep `.env` out of git.
- Ensure DNS `A/AAAA` for `PUBLIC_DOMAIN` points to this host.
- Open firewall inbound `80` and `443`.
