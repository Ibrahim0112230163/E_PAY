# Docker Sandbox Guide

This project can run as a Dockerized pentest target behind an Nginx TLS reverse proxy. The app container builds the React frontend, serves it through the Flask backend, and Nginx exposes the sandbox over HTTPS.

## Start The Sandbox

From the repository root:

```powershell
docker compose up --build
```

Open:

```text
https://localhost
```

The TLS certificate is self-signed, so your browser will show a certificate warning. Accept it only for this local sandbox.

Health check:

```powershell
curl -k https://localhost/health
```

Expected response:

```json
{"message":"E-Banking API is running","status":"ok"}
```

## Environment Files

Docker Compose loads backend secrets from:

```text
e_banking/backend/.env.backend
```

The Docker frontend build uses same-origin API calls by default, so browser requests stay on HTTPS through Nginx.

For non-Docker local development, `frontend/.env` can still use:

```text
http://localhost:5001
```

To use another HTTPS host port:

```powershell
$env:NGINX_SSL_PORT="8443"
$env:VITE_BACKEND_URL=""
docker compose up --build
```

Then open:

```text
https://localhost:8443
```

## Reset The Sandbox

```powershell
docker compose down
docker compose up --build
```

## Run OWASP ZAP Baseline

The compose file includes an optional ZAP service for baseline scanning.

```powershell
docker compose --profile security-tools up --build zap
```

The report is written to:

```text
zap-reports/zap-baseline-report.html
```

## Notes For Penetration Testing

The app, Nginx, and security tooling share a private Docker network named `e-pay-sandbox`. From another container on that network, the HTTPS target URL is:

```text
https://nginx
```

From your host machine, the target URL is:

```text
https://localhost
```
