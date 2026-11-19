# HashSolo Dokploy Compose

Komplettes Docker-Compose-Setup für **Dokploy** inkl. **Bitcoin Core** (`bitcoind`), **Backend** (`hashsolo`) und **Frontend** (`hashsolo-ui`).

> **Fork-Hinweis:** Dieses Setup deployt die gehärteten und modernisierten Forks **[gOOvER/hashsolo](https://github.com/gOOvER/hashsolo)** und **[gOOvER/hashsolo-ui](https://github.com/gOOvER/hashsolo-ui)** (basierend auf [`benjamin-wilson/public-pool`](https://github.com/benjamin-wilson/public-pool) und [`benjamin-wilson/public-pool-ui`](https://github.com/benjamin-wilson/public-pool-ui)).

## Enthalten im Stack

- **`bitcoind` (`bitcoin/bitcoin:latest`):** Integrierter Bitcoin-Core-Node (standardmäßig pruned auf `550` MB ≈ ~10 GB Disk) mit RPC & ZMQ (`-printtoconsole=0 -debug=0 -nodebuglogfile`).
- **`hashsolo` (`ghcr.io/goover/hashsolo:latest`):** Stratum-V1-Backend (Port `3333`) + API (intern `3334`, inkl. `/api/node/status` für den Live-Sync-Status).
- **`hashsolo-ui` (`ghcr.io/goover/hashsolo-ui:latest`):** Angular-22-Dashboard mit Caddy Reverse-Proxy (`/api/*` → `hashsolo:3334`) und Brotli/Gzip-Komprimierung.
- Alle Services nutzen `pull_policy: always`, sodass ein **Redeploy** in Dokploy automatisch die neuesten `:latest`-Images zieht.

## Einrichtung in Dokploy

1. **Docker-Compose-Projekt** in Dokploy anlegen und dieses Repository (`https://github.com/gOOvER/hashsolo-dokploy`) verwenden.
2. **Environment Variables** im Tab **Environment** setzen (Vorlage: [`.env.example`](.env.example)):
   - `BITCOIN_RPC_USER` & `BITCOIN_RPC_PASSWORD` (starkes Passwort wählen – wird automatisch für `bitcoind` und `hashsolo` genutzt)
   - `BITCOIN_PRUNE` (`550` = ~10 GB pruned Node als Standard, `0` = Full Node mit ~700+ GB)
   - `PUBLIC_POOL_STRATUM_URL=stratum.goover.dev:3333`
   - `DEV_FEE_ADDRESS=` *(optional: setzt 1,5 % Fee für Miner ≥ 50 TH/s; unter 50 TH/s bleibt es 0 %)*
3. **Domain in Dokploy** (Tab **Domains** → **Add Domain**):
   - **Service:** `hashsolo-ui`, **Port:** `80`, **Host:** `hashsolo.goover.dev`, **HTTPS:** Let's Encrypt.
   - Caddy proxiet `/api/*` automatisch intern an `hashsolo:3334`.
4. **Ports / Firewall:**
   - **`3333/tcp`**: Stratum für Miner (muss in der Server-Firewall offen sein).
   - **`8333/tcp`**: Bitcoin P2P-Netzwerk (optional offen für eingehende Peers).
5. **Persistenz (Volumes):**
   - `bitcoind-data` (`/home/bitcoin/.bitcoin`)
   - `hashsolo-db` (`/hashsolo/DB`)

## Miner-Konfiguration (Beispiel Bitaxe / NMMiner)

- **Pool URL:** `stratum.goover.dev` *(oder `stratum+tcp://stratum.goover.dev:3333` bei Minern mit kombiniertem Feld)*
- **Port:** `3333`
- **Username / Wallet:** `<deine-btc-adresse>.<workername>` (z. B. `bc1q...xyz.nmminer1`)
- **Passwort:** `x`
