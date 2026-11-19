# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

## [1.0.0] - 2026-11-19

### Added
- Initial `v1.0.0` release of the **HashSolo Dokploy** Compose stack (`bitcoind` + `hashsolo` + `hashsolo-ui`).
- Configured `bitcoind` with `-zmqpubhashblock=tcp://0.0.0.0:3000`, `pruned` support, and RPC credentials via `.env`.
- Configured `hashsolo` with `STRATUM_START_DIFF=0.001` default (ideal for KH/s–MH/s ESP miners and TH/s ASICs), `DEV_FEE_ADDRESS`, Telegram, and `ntfy` environment variables.
- Configured `hashsolo-ui` with built-in Caddy `/api/*` reverse proxy (`API_UPSTREAM=hashsolo:3334`) and `pull_policy: always`.
