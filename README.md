# Acme Portal — Active Recon Demo Target

A **deliberately leaky static site** for practicing active reconnaissance in a classroom
(CS24/L — AI-Driven Pentesting and Ethical Hacking). Everything here is a harmless prop:
the "credentials," version numbers, and internal IP are fake and lead nowhere.

> **Authorized use only.** You own this copy. Serve it on `localhost` and scan only `localhost`.
> Do not point these tools at any host you do not own or have written permission to test.

## Run it

```bash
git clone https://github.com/Alteyae/acme-portal-demo.git
cd acme-portal-demo
python3 -m http.server 8000
```

Then browse to `http://localhost:8000`.

## Recon it (from another terminal)

```bash
# Banner grab — what software is this?
curl -sI http://localhost:8000/

# robots.txt — what are they trying to hide?
curl -s http://localhost:8000/robots.txt

# Leaky status endpoint
curl -s http://localhost:8000/api/status.json

# TCP connect — is the port open?
nc -zv localhost 8000

# Service/version detection (Kali)
nmap -sV -p 8000 localhost

# Path discovery (Kali)
gobuster dir -u http://localhost:8000 -w /usr/share/wordlists/dirb/common.txt
```

## What each technique should reveal

| Technique | Breadcrumb |
|---|---|
| Banner grab (`curl -sI`) | `Server: SimpleHTTP/0.6 Python/3.x` — exact software + version |
| robots.txt | Discloses `/admin.html`, `/api/`, `/backup/` |
| api/status.json | `environment: production`, Postgres/Redis versions |
| View source (DevTools) | Staging IP comment + fake `demo/demo` note in `/admin.html` |
| Path discovery | `/admin.html`, `/sitemap.xml` return 200; junk returns 404 |

## Teardown

```bash
# stop the server: Ctrl+C in its terminal, then
cd .. && rm -rf acme-portal-demo
```
