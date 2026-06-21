# Local Nitter API

This repo runs a local Nitter instance for searching and reading X/Twitter data through a simple local API.

It uses:

- Docker Compose
- Redis
- Oxylabs proxy settings from `.env`
- X/Twitter logged-in cookie sessions from `sessions.jsonl`
- an internal `nitter-api-proxy` sidecar for X GraphQL requests

## Do Not Commit Secrets

These files are local-only and should never be pushed to GitHub:

```text
.env
sessions.jsonl
accounts.json
accounts*.json
```

Ask the project owner for `.env` and `sessions.jsonl` through a secure channel.

## Requirements

Install Docker Desktop.

## Setup

Clone the repo:

```bash
git clone <repo-url>
cd nitter-1
```

Create local config:

```bash
cp nitter.example.conf nitter.conf
```

Put the provided `.env` and `sessions.jsonl` files in the repo root.

Confirm these local files exist:

```bash
ls .env nitter.conf sessions.jsonl
```

## Run

```bash
docker compose up -d --build
```

Open the local site:

```text
http://127.0.0.1:8080
```

## Test The API

Health:

```bash
curl -fsS http://127.0.0.1:8080/api/v1/health
```

Expected: JSON with `"ok": true` and at least one cookie session.

Profile lookup:

```bash
curl -i http://127.0.0.1:8080/api/v1/users/jack
```

Expected: `HTTP/1.1 200 OK` and JSON for the user.

Search:

```bash
curl -i --get http://127.0.0.1:8080/api/v1/search/tweets \
  --data-urlencode 'q="Owner.com" restaurant'
```

Expected: `HTTP/1.1 200 OK` and tweet JSON.

Browser search:

```text
http://127.0.0.1:8080/search?f=tweets&q=%22Owner.com%22+restaurant
```

Expected: a normal Nitter search page.

## Useful API Endpoints

```text
GET /api/v1/health
GET /api/v1/users/:username
GET /api/v1/search/tweets?q=<query>
```

Examples:

```bash
curl http://127.0.0.1:8080/api/v1/users/jack
curl --get http://127.0.0.1:8080/api/v1/search/tweets --data-urlencode 'q=restaurant'
```

## Restart After Secret Changes

If `.env`, `nitter.conf`, or `sessions.jsonl` changes:

```bash
docker compose down -v
docker compose build nitter-config
docker compose up -d --no-build
```

If source code changes:

```bash
docker compose down -v
docker compose up -d --build
```

## Logs

```bash
docker compose ps
docker compose logs --tail=100 nitter
docker compose logs --tail=100 nitter-api-proxy
```

## Notes

The account session file is effectively logged-in account access. Keep it private.

The default limits are intentionally conservative. If the instance says no sessions are available or rate limited, wait before retrying or add more healthy authorized sessions.
