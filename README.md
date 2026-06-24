# BeaconWick

[![Release](https://img.shields.io/github/v/release/webappsgo/beaconwick)](https://github.com/webappsgo/beaconwick/releases)
[![License](https://img.shields.io/github/license/webappsgo/beaconwick)](LICENSE.md)

A self-hosted status page and uptime monitoring tool. Monitor HTTP endpoints, TCP
ports, databases, message queues, and application services. Publish public status
pages on custom domains. Deploy an agent on any host to collect system metrics and
monitor local services without exposing internal ports. Scales from a single
self-hosted instance to a multi-tenant PaaS deployment — single binary, zero
required configuration.

🌐 **Site:** https://beaconwick.example.com

---

## ✨ Features

- **Uptime monitoring** — HTTP/HTTPS (keyword, JSON assertion, TLS cert expiry,
  domain expiry), TCP, UDP, DNS, ICMP, SNMP v1/v2c/v3, WebSocket, gRPC, MQTT,
  Kafka, RabbitMQ, RADIUS, SIP, SMTP, IMAP, POP3, FTP, SSH, Tailscale peers
- **Database monitors** — PostgreSQL, MariaDB, MySQL, MSSQL, Oracle, MongoDB,
  Redis, Memcached
- **Virtual machine and container monitors** — Docker, Incus, libvirt/KVM/QEMU
- **Heartbeat monitors** — ping-in model for cron jobs and background tasks
- **Real browser monitoring** — headless browser engine for JavaScript-rendered pages
- **Agent** — deploy directly on any Linux, macOS, Windows, or BSD host; streams
  CPU, memory, disk, and network telemetry; discovers running services per platform
  and proxies checks locally so internal ports stay closed
- **Tor and I2P** — monitor .onion and .i2p endpoints; admin panel reachable via
  .onion hidden service when Tor is present
- **Multiple status pages** — unlimited pages, each on its own sub-path or custom
  domain; always public, no authentication required
- **Component groups** — five-state components (Operational → Major Outage) with
  automatic page-level roll-up; 90-day uptime history per component
- **Incident management** — Investigating → Identified → Monitoring → Resolved
  lifecycle; templates; postmortems; advance maintenance reminders
- **Notifications** — email, SMS, voice, team chat (Slack, Discord, Teams,
  Mattermost, Google Chat, and more), messaging apps (Telegram, WhatsApp, Signal,
  Matrix, and more), mobile push (Ntfy, Gotify, Pushover, Bark, and more), browser
  push, on-call platforms (PagerDuty, OpsGenie, Grafana OnCall, and more),
  ticketing/ITSM, webhooks, and Apprise (78+ additional providers)
- **Monitor dependencies** — suppress child alerts when a parent is down
- **Maintenance windows** — one-time or recurring; suppresses alerts and shows
  "scheduled maintenance" on status pages
- **Embeddable widgets** — SVG status badges and JavaScript status widget per
  monitor and status page
- **SLA reporting** — 30/60/90-day uptime percentages; periodic digest emails
- **Internationalization** — multi-language admin UI and status pages
- **Organizations** — full org isolation for multi-tenant and PaaS deployments
- **Cluster mode** — multi-node high-availability deployment
- **REST API** — full CRUD for all resources; token authenticated

---

## 📦 Install

Download the latest release from [GitHub Releases](https://github.com/webappsgo/beaconwick/releases/latest).

### Server

#### Linux

| Arch | Binary |
|------|--------|
| amd64 | `beaconwick-linux-amd64` |
| arm64 | `beaconwick-linux-arm64` |

```bash
curl -LSsf https://github.com/webappsgo/beaconwick/releases/latest/download/beaconwick-linux-amd64 \
  -o /usr/local/bin/beaconwick && chmod +x /usr/local/bin/beaconwick
beaconwick --serve
```

#### macOS

| Arch | Binary |
|------|--------|
| Intel (x86_64) | `beaconwick-darwin-amd64` |
| Apple Silicon (arm64) | `beaconwick-darwin-arm64` |

```bash
ARCH=$(uname -m | sed 's/x86_64/amd64/;s/aarch64/arm64/')
curl -LSsf "https://github.com/webappsgo/beaconwick/releases/latest/download/beaconwick-darwin-${ARCH}" \
  -o /usr/local/bin/beaconwick && chmod +x /usr/local/bin/beaconwick
xattr -d com.apple.quarantine /usr/local/bin/beaconwick 2>/dev/null || true
beaconwick --serve
```

#### Windows

| Arch | Binary |
|------|--------|
| amd64 | `beaconwick-windows-amd64.exe` |
| arm64 | `beaconwick-windows-arm64.exe` |

Download and add to `%PATH%`, then run `beaconwick.exe --serve`.

#### FreeBSD

| Arch | Binary |
|------|--------|
| amd64 | `beaconwick-freebsd-amd64` |
| arm64 | `beaconwick-freebsd-arm64` |

```bash
curl -LSsf https://github.com/webappsgo/beaconwick/releases/latest/download/beaconwick-freebsd-amd64 \
  -o /usr/local/bin/beaconwick && chmod +x /usr/local/bin/beaconwick
beaconwick --serve
```

### Agent

The agent runs directly on any monitored host — **never inside a container**.
Install on every host you want to monitor at the system level.

#### Linux / macOS / FreeBSD

```bash
# Linux amd64 example — adjust OS and arch as above
curl -LSsf https://github.com/webappsgo/beaconwick/releases/latest/download/beaconwick-agent-linux-amd64 \
  -o /usr/local/bin/beaconwick-agent && chmod +x /usr/local/bin/beaconwick-agent
beaconwick-agent --server https://your-beaconwick-instance --token <agent-token>
```

#### Windows

```powershell
# Download beaconwick-agent-windows-amd64.exe and add to %PATH%
beaconwick-agent.exe --server https://your-beaconwick-instance --token <agent-token>
```

The agent token is generated in the BeaconWick admin panel under **Agents → Add Agent**.

---

## 🐳 Docker

```bash
curl -LSsf https://github.com/webappsgo/beaconwick/raw/main/docker/docker-compose.yml \
  -o docker-compose.yml
docker compose up -d
```

The server is available at `http://localhost:8080`. Open it in your browser to
complete first-run setup.

**Image:** `ghcr.io/webappsgo/beaconwick:latest`

> **Note:** Do not run the agent inside Docker. Install it directly on the host
> using the binary above.

---

## 🖥️ Server CLI

```
beaconwick [flags] [command]
```

| Flag / Command | Description |
|---|---|
| `--serve` | Start the server (default) |
| `--port <n>` | Listen port (default: 8080) |
| `--config <path>` | Config file path |
| `--debug` | Enable debug mode |
| `--maintenance backup [file]` | Back up config and database |
| `--maintenance restore <file>` | Restore from backup |
| `--version` | Print version and exit |

---

## 🤖 Agent CLI

```
beaconwick-agent [flags]
```

| Flag | Description |
|---|---|
| `--server <url>` | BeaconWick server URL (required) |
| `--token <token>` | Agent registration token (required) |
| `--interval <duration>` | Telemetry push interval (default: 30s) |
| `--config <path>` | Config file path |
| `--version` | Print version and exit |

---

## ⚙️ Configuration

BeaconWick is configured via `server.yml` (auto-generated on first run) and the
web-based setup wizard. Key options:

| Setting | Default | Description |
|---|---|---|
| `server.port` | `8080` | HTTP listen port |
| `server.fqdn` | auto-detected | Public hostname |
| `server.registration.mode` | `admin_only` | `open` / `invite` / `admin_only` / `disabled` |
| `server.notifications.email.smtp.host` | auto-detected | SMTP server |
| `server.tor.binary` | auto-detected | Path to Tor binary |
| `server.tor.use_network` | `false` | Route outbound checks through Tor |

Full configuration reference: [https://beaconwick.example.com/docs/config](https://beaconwick.example.com/docs/config)

---

## 🔌 API

All management actions are available via the REST API. Base URL:
`https://your-instance/api/v1/`

Full API reference: [https://beaconwick.example.com/docs/api](https://beaconwick.example.com/docs/api)

---

## 🛠️ Development

**Prerequisites:** Go 1.22+, Docker, Make

```bash
git clone https://github.com/webappsgo/beaconwick
cd beaconwick
make dev        # build and run in development mode
make test       # run test suite
make lint       # run linters
```

| Target | Description |
|---|---|
| `make build` | Build all binaries |
| `make release` | Build release binaries for all platforms |
| `make test` | Run unit and integration tests |
| `make lint` | Run linters |
| `make dev` | Build and run in development mode |
| `make clean` | Remove build artifacts |

### 🐳 Docker build

```bash
make docker     # build and run via Docker
docker buildx build -f docker/Dockerfile -t ghcr.io/webappsgo/beaconwick:devel .
```

---

## ⚠️ Disclaimer

This software is provided "as is" without warranty of any kind. Use at your own risk.

- **No Warranty**: The authors are not responsible for any damages, data loss, or
  issues arising from use of this software
- **Not Professional Advice**: This software does not constitute legal, financial,
  medical, or other professional advice
- **Third-Party Services**: Notification providers, monitoring targets, and any
  external services connected to BeaconWick are subject to their own terms of service
- **Security**: While we strive to follow security best practices, no software is
  guaranteed to be free of vulnerabilities
- **Production Use**: Evaluate thoroughly before deploying in production environments

By using this software, you acknowledge that you have read and understood this disclaimer.

---

## 📄 License

MIT — see [LICENSE.md](LICENSE.md)
