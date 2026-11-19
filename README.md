# HashSolo Dokploy Compose

Komplettes Docker-Compose-Setup für **Dokploy** inkl. **Bitcoin Core** (`bitcoind`), **Backend** (`hashsolo`) und **Frontend** (`hashsolo-ui`).

## Einrichtung in Dokploy

1. **Docker-Compose-Projekt** in Dokploy anlegen und dieses Repository (`https://github.com/gOOvER/hashsolo-dokploy`) verwenden.
2. **Environment Variables** im Tab **Environment** setzen:
   - `BITCOIN_RPC_USER` & `BITCOIN_RPC_PASSWORD` (starkes Passwort wählen – wird automatisch für `bitcoind` und `hashsolo` genutzt)
   - `BITCOIN_PRUNE` (`550` = ~10 GB pruned Node als Standard, `0` = Full Node mit ~700+ GB)
   - `PUBLIC_POOL_STRATUM_URL=stratum.goover.dev:3333`
3. **Domain in Dokploy** (Tab **Domains** → **Add Domain**):
   - **Service:** `hashsolo-ui`, **Port:** `80`, **Host:** `hashsolo.goover.dev`, **HTTPS:** Let's Encrypt.
   - Caddy proxiet `/api/*` automatisch intern an `hashsolo:3334`.
4. **Ports / Firewall:**
   - **`3333/tcp`**: Stratum für Miner (muss in der Server-Firewall offen sein).
   - **`8333/tcp`**: Bitcoin P2P-Netzwerk (optional offen für eingehende Peers).
5. **Persistenz:**
   - `bitcoind-data` (`/home/bitcoin/.bitcoin`)
   - `hashsolo-db` (`/hashsolo/DB`)
