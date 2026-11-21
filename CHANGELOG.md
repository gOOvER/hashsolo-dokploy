# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

### Added
- Exposed optional **Stratum V1 TLS** (`STRATUM_TLS_ENABLED=false`, port `4333`) and **Stratum V2** (`STRATUM_V2_ENABLED=false`, port `23330`) ports and environment variables in `docker-compose.yml` and `.env.example`.

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
