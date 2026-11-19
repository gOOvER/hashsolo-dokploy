# HashSolo Dokploy Compose

Docker-Compose-Setup für **Dokploy** (Backend `hashsolo` + Frontend `hashsolo-ui`).

## Einrichtung in Dokploy

1. **Docker-Compose-Projekt** in Dokploy anlegen und diese `docker-compose.yml` verwenden.
2. **Environment Variables** aus `.env.example` im Dokploy-Dashboard hinterlegen (`BITCOIN_RPC_*`, `PUBLIC_POOL_API_URL`, `PUBLIC_POOL_STRATUM_URL`, `CORS_ORIGIN`, optional `SHARE_SUBMISSION_API_KEY`).
3. **Domains / Routing in Dokploy:**
   - **Web-UI + API (eine Domain):** Hauptdomain (z. B. `https://pool.example.com`) in Dokploy auf Service `hashsolo-ui` (Port `80`) routen. Caddy proxiet `/api/*` automatisch intern an `hashsolo:3334` (`PUBLIC_POOL_API_URL` kann leer bleiben).
   - **Stratum TCP (`hashsolo`, Port `3333`):** Wird direkt über `${STRATUM_PORT:-3333}:3333/tcp` am Host bereitgestellt (Firewall-Port `3333/tcp` freigeben).
   - *(Optional)* Falls du die API separat erreichbar machen willst, kannst du zusätzlich eine Subdomain auf `hashsolo` (Port `3334`) routen und in `PUBLIC_POOL_API_URL` + `CORS_ORIGIN` eintragen.
4. **Persistenz:** Die SQLite-Datenbank liegt im benannten Docker-Volume `hashsolo-db` (`/hashsolo/DB`).
