# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

### Added
- Added `prometheus.io/scrape`, `prometheus.io/port`, and `prometheus.io/path=/metrics` labels plus configurable `PROMETHEUS_NETWORK_ALIAS` (`hashsolo-metrics` on `dokploy-network`) to `hashsolo` in `docker-compose.yml` for plug-and-play Prometheus scraping.
- Exposed configurable backend & UI security ENVs in `docker-compose.yml` and `.env.example` (`ALLOW_PUBLIC_METRICS=false`, `METRICS_BEARER_TOKEN`, `STRATUM_MAX_CONNECTIONS_PER_IP=100`, `STRATUM_MAX_INVALID_MESSAGES=20`, `API_RATE_LIMIT_MAX=120`, `API_RATE_LIMIT_WINDOW_MS=60000`).
- Added `json-file` log rotation (`10m` x `3`), configurable memory limits (`BITCOIND_MEM_LIMIT`, `HASHSOLO_MEM_LIMIT`, `HASHSOLO_UI_MEM_LIMIT`), `bitcoind` tuning ENVs (`BITCOIN_DBCACHE=512`, `BITCOIN_MAXCONNECTIONS=40`), and an optional `backup` profile (`hashsolo-backup` daily SQLite `.backup` with 7-day retention).

### Changed
- Enabled `bitcoind` console logging (`-printtoconsole=1` with `json-file` log rotation), disabled unused Tor control polling (`-listenonion=0`), increased `stop_grace_period` to `120s` (prevents `SIGKILL` during `dbcache` flush and slow block verification on restart), and raised `bitcoind` healthcheck `start_period` to `60s`.

### Security
- Sandboxed `bitcoind` (`no-new-privileges:true`, `cap_drop: [ALL]` with `cap_add: [CHOWN, FOWNER, DAC_OVERRIDE, SETUID, SETGID]` for `/entrypoint.sh` `gosu`/`chmod` setup), `hashsolo` (`security_opt: [no-new-privileges:true]`, `cap_drop: [ALL]`, `read_only: true`, `tmpfs: [/tmp]`), and `hashsolo-ui` (`security_opt: [no-new-privileges:true]`, `cap_drop: [ALL]`, `cap_add: [NET_BIND_SERVICE]`) in `docker-compose.yml`.
- Disabled public `/api/metrics` access through `hashsolo-ui` by default (`ALLOW_PUBLIC_METRICS=false`) while keeping internal Prometheus scraping via `hashsolo-metrics:3334/metrics` active.

## [1.2.0] - 2026-11-21

### Added
- Exposed optional **Stratum V1 TLS** (`STRATUM_TLS_ENABLED=false`, port `4333`) and **Stratum V2** (`STRATUM_V2_ENABLED=false`, port `23330`) ports and environment variables in `docker-compose.yml` and `.env.example`, plus Traefik TCP TLS labels (`hashsolo-stratum-tls` via `dokploy-network` and `STRATUM_TLS_DOMAIN`) on `hashsolo` without binding host port `4333`.

## [1.1.0] - 2026-11-21

### Added
- Added Docker `healthcheck` definitions for `bitcoind` (`bitcoin-cli getblockchaininfo`) and `hashsolo-ui` (`wget http://127.0.0.1:80/`) in `docker-compose.yml`.
- Exposed `DONATION_BTC_ADDRESS`, `DONATION_URL`, `DONATION_LINK_LABEL`, `DONATION_TITLE`, and `DONATION_SUBTITLE` runtime footer ENVs for `hashsolo-ui` in `docker-compose.yml` and `.env.example`.

### Fixed
- Removed duplicate `-debug=0` and `-nodebuglogfile` flags from the `bitcoind` command in `docker-compose.yml`.

### Changed
- Updated `hashsolo-ui` `depends_on.hashsolo.condition` to `service_healthy` in `docker-compose.yml`.
- Switched `bitcoind` ZMQ publisher in `docker-compose.yml` from `zmqpubrawblock` to lightweight 45-byte `zmqpubhashblock`.
- Updated `README.md` with GitHub Wiki links, Discord Components V2 bot, and current UI/backend features.

## [1.0.0] - 2026-11-19

### Added
- Initial `v1.0.0` release of the **HashSolo Dokploy** Compose stack (`bitcoind` + `hashsolo` + `hashsolo-ui`).
- Configured `bitcoind` with `-zmqpubhashblock=tcp://0.0.0.0:3000`, `pruned` support, and RPC credentials via `.env`.
- Configured `hashsolo` with `STRATUM_START_DIFF=0.001` default (ideal for KH/s–MH/s ESP miners and TH/s ASICs), `DEV_FEE_ADDRESS`, Telegram, and `ntfy` environment variables.
- Configured `hashsolo-ui` with built-in Caddy `/api/*` reverse proxy (`API_UPSTREAM=hashsolo:3334`) and `pull_policy: always`.
