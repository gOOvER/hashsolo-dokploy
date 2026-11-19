# HashSolo Dokploy Compose

Docker-Compose-Setup für **Dokploy** (Backend `hashsolo` + Frontend `hashsolo-ui`).

## Einrichtung in Dokploy

1. **Docker-Compose-Projekt** in Dokploy anlegen und diese `docker-compose.yml` verwenden.
2. **Environment Variables** aus `.env.example` im Dokploy-Dashboard hinterlegen (`BITCOIN_RPC_*`, `PUBLIC_POOL_API_URL`, `PUBLIC_POOL_STRATUM_URL`, `CORS_ORIGIN`, optional `SHARE_SUBMISSION_API_KEY`).
3. **Domains / Routing in Dokploy:**
   - **Frontend (`hashsolo-ui`, Port `80`):** Hauptdomain (z. B. `https://pool.example.com`) auf Service `hashsolo-ui` (Port `80`) routen.
   - **Backend API (`hashsolo`, Port `3334`):** API-Subdomain (z. B. `https://api.pool.example.com`) auf Service `hashsolo` (Port `3334`) routen und in `PUBLIC_POOL_API_URL` sowie `CORS_ORIGIN` eintragen.
   - **Stratum TCP (`hashsolo`, Port `3333`):** Wird direkt über `${STRATUM_PORT:-3333}:3333/tcp` am Host bereitgestellt (Firewall-Port `3333/tcp` freigeben).
4. **Persistenz:** Die SQLite-Datenbank liegt im benannten Docker-Volume `hashsolo-db` (`/hashsolo/DB`).
