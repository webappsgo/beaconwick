## Project description

BeaconWick is a self-hosted status page and uptime monitoring tool. It lets teams
monitor the availability of HTTP endpoints, TCP ports, DNS records, and ICMP targets,
publish a public-facing status page, and manage incidents — all from a single
self-contained binary.

For server-level health (CPU, memory, disk, network), BeaconWick deploys a lightweight
agent on each monitored host. The agent streams host metrics back to the central server,
providing the same depth of visibility as tools like Beszel without requiring a separate
monitoring stack.

The project is inspired by Uptime Kuma (simple self-hosted uptime monitoring), Beszel
(agent-based server metrics), and Atlassian Statuspage (public incident communication).
It replaces all three with a single tool that covers both synthetic checks and real
host telemetry.

## Project variables

project_name:     beaconwick
project_org:      webappsgo
internal_name:    beaconwick
internal_org:     webappsgo
app_name:         BeaconWick
official_site:    beaconwick.example.com
maintainer_name:  casjay
maintainer_email: casjay@yahoo.com

## Business logic

**Target users:**
- Individual self-hosters running a personal uptime monitor and status page
- DevOps engineers and sysadmins monitoring infrastructure and services
- Development teams tracking API and web service availability
- End users (anonymous) viewing the public status page
- On-call engineers managing and communicating incidents
- SMBs running a shared instance for their team
- Operators running BeaconWick as a PaaS — offering status page and uptime
  monitoring as a hosted service to their own customers

**Features:**

- **Synthetic uptime monitoring**: check HTTP/HTTPS endpoints (with custom
  headers, authentication, and accepted status codes), TCP ports, UDP ports, DNS
  resolution, and ICMP ping targets for availability and response time; HTTP
  monitors also support JSON path/field assertion to validate API responses
- **Keyword and certificate checks**: detect expected or forbidden text in HTTP
  responses; alert when TLS certificates are near expiry or already expired;
  separately track domain registration expiry and alert at configurable thresholds
- **Response-time alerting**: alert when a monitor's response time exceeds a
  configurable threshold, independent of whether the target is up or down
- **Real browser monitoring**: optionally run HTTP checks through a headless
  browser engine to verify pages that require JavaScript rendering
- **Database monitors**: test connectivity and query response for PostgreSQL,
  MariaDB, MySQL, MSSQL, Oracle, MongoDB, Redis, and Memcached targets
- **Application protocol monitors**: verify reachability of SMTP, IMAP, POP3,
  FTP, SSH, SIP (OPTIONS keepalive), WebSocket (HTTP upgrade handshake), gRPC
  (health-check protocol), MQTT (publish/subscribe), Kafka (producer check),
  RabbitMQ (node health), and RADIUS endpoints
- **SNMP monitors**: poll any SNMP OID on a network device and assert the
  returned value; supports SNMP v1/v2c/v3
- **Tailscale peer monitors**: check reachability of a Tailscale peer node
- **Manual status**: admin may force any monitor to UP, DOWN, or a degraded state
  without an active check; useful for communicating known issues before automation
  catches them
- **Monitor tags**: assign colour-coded tags to monitors for grouping, filtering,
  and bulk operations in the admin dashboard
- **Per-monitor proxy support**: each HTTP/HTTPS monitor may route its checks
  through a proxy; supports HTTP, HTTPS, and SOCKS proxies
- **Tor and I2P monitoring**: monitors may route checks over the Tor network to
  reach .onion hidden services, or over I2P to reach .i2p eepsites; enables
  monitoring of anonymous-network endpoints alongside clearnet services
- **Docker container monitors**: check container running state and embedded health
  check status on the local or a remote Docker host
- **Incus instance monitors**: check instance running state and health for
  containers and virtual machines managed by a local or remote Incus server
- **libvirt VM monitors**: check domain running state for virtual machines managed
  by a local or remote libvirt hypervisor (KVM, QEMU)
- **Agent-based host monitoring**: deploy an agent binary directly on any host
  (Linux, macOS, Windows, BSD) — the agent must run on the host itself, never
  inside a container; it continuously streams host telemetry (CPU, memory, disk,
  network) and host identity (platform, OS family, distro name and version where
  applicable, kernel version) back to the server — this data is private and
  visible only to authenticated users, never on a public status page
- **Agent service discovery**: the agent is platform and distro aware and
  discovers the following resources per platform:
  - **Linux** — systemd and SysV/init services; Docker containers; Incus
    instances; libvirt/KVM/QEMU domains; databases (PostgreSQL, MariaDB, MySQL,
    MongoDB, Redis, Memcached); common daemons (nginx, Apache, Postfix, Dovecot,
    Exim, OpenSSH, HAProxy, etc.); ZFS pool health; package update availability
  - **BSD (FreeBSD, OpenBSD, NetBSD)** — rc services; FreeBSD jails; bhyve VMs;
    pf firewall state; ZFS pool health; databases and common daemons (same as
    Linux where available)
  - **macOS** — launchd services; Homebrew services; Docker Desktop containers;
    common daemons; macOS sharing services (file sharing, remote login, etc.)
  - **Windows** — Windows Services (SCM); Hyper-V VMs; Active Directory and
    Domain Controller health; DNS Server; DHCP Server; IIS sites and app pools;
    MSSQL instances; Windows Defender status; Windows Update pending state;
    Scheduled Tasks; RDP service; WSUS
  - **All platforms** — network interface state; open/listening ports; disk
    health (SMART where available); running process inventory
- **Agent-proxied checks**: when a monitor is backed by an agent, the server
  pushes the check configuration (what to check and at what interval) to the
  agent; the agent runs its own scheduler and executes each check independently
  at the configured interval, then reports results back to the server — internal
  ports never need to be exposed to the internet; each service on an agent host
  may have a different check interval (e.g. Postfix every 15 min, MariaDB every
  1 min, Dovecot every 5 min)
- **Multiple status pages**: create any number of named status pages, each
  accessible at its own sub-path and optionally mapped to a custom domain; when a
  request arrives with a matching hostname the correct page is served automatically;
  status pages are always publicly accessible — no authentication, no password gates
- **Public status page**: each status page is human-readable, mobile-responsive,
  shows current component health, active incidents, and uptime history; no login
  required to view
- **Component statuses**: each component on a status page reports one of five
  states — Operational, Degraded Performance, Partial Outage, Major Outage, or
  Under Maintenance; the page-level status rolls up automatically from its
  components
- **Component groups**: organise monitors into named service components (e.g.
  "API", "Database", "CDN") that appear as cards on the status page; each status
  page can include its own subset of components
- **Maintenance windows**: schedule planned downtime for any monitor or component;
  during a window monitors do not alert and the status page shows "scheduled
  maintenance" instead of "down"; windows may be one-time or recurring; advance
  reminder notifications are sent to subscribers before the window begins
- **Heartbeat monitors**: the inverse of outbound checks — a service, cron job,
  or backup script pings BeaconWick at a defined interval; if the ping does not
  arrive within the expected window the monitor is marked down; used to track
  scheduled tasks and background jobs that have no externally reachable endpoint
- **Monitor dependencies**: a monitor may declare another monitor as its parent;
  if the parent is down, the child's alerts are suppressed — prevents alert storms
  when a single network or upstream failure cascades across many monitors
- **Incident management**: incidents follow a lifecycle of Investigating →
  Identified → Monitoring → Resolved; each incident has timestamped update posts,
  is linked to affected components, and is shown on the status page; supports
  incident templates for faster first response and postmortem reports published
  after resolution
- **Escalating notifications**: alert after a monitor has been down for N minutes,
  then repeat every M minutes until resolved; configurable per monitor
- **Custom branding per status page**: each status page supports its own logo,
  accent colour, and title; pages on custom domains should feel like the owner's
  brand, not BeaconWick's; each page may opt out of search engine indexing
- **Embeddable widgets**: each monitor and status page exposes an embeddable SVG
  status badge (up/down/degraded, optional response time or uptime percentage) and
  a JavaScript status widget for embedding in help desks, dashboards, or apps
- **Notifications**: send alerts on status changes and incident updates across
  multiple channel categories: email, SMS and voice call, team chat, messaging
  apps, mobile push, browser push, on-call and incident management platforms,
  ticketing and ITSM platforms, generic webhooks, and a meta-provider that gives
  access to dozens of additional services through a single integration; webhook
  output must be compatible with the Uptime Kuma outgoing webhook schema
- **Status history and SLA reporting**: store uptime and response-time history;
  calculate and display uptime percentages over 30/60/90-day windows
- **Subscriber notifications**: anonymous users may subscribe to status page
  updates by email; subscribers may opt in to specific components rather than the
  whole page; notifications are sent on incident open, update, resolve, and before
  scheduled maintenance windows; subscriber lists may be imported and exported as CSV
- **Uptime digest reports**: periodic email reports summarising uptime percentages
  and incident history across all monitors; configurable frequency per recipient
- **Config import and export**: back up and restore all monitors, status pages,
  and notification settings as a portable JSON file; supports importing from
  Uptime Kuma JSON exports
- **Multi-user management**: admin and read-only operator roles; admins manage
  monitors, agents, incidents, and users; operators may acknowledge and update
  incidents but cannot change configuration
- **Organizations**: multiple isolated organizations may exist on a single
  instance; each org has its own monitors, agents, status pages, users, and
  notification channels with no cross-org visibility; enables SMB team isolation
  and PaaS multi-tenancy on a shared deployment
- **Public user registration**: user self-registration is supported; enables
  operating BeaconWick as a PaaS offering where customers sign up and manage
  their own org, monitors, and status pages
- **Internationalization**: the admin dashboard, agent UI, and every public status
  page must support multiple languages; language is detected from browser preference
  and may be overridden per user; status pages inherit the instance default but
  may have their own language set independently
- **API access**: all management actions available via a documented REST API with
  token authentication; enables integration with CI/CD pipelines and external tools

**Constraints and non-negotiables:**
- Must run as a single self-contained binary with no mandatory external runtime
  dependencies
- Supports clustered multi-node deployment for high availability and horizontal
  scaling; a single-node deployment must work with zero configuration
- First-run must work with zero configuration
- The public status page must load without JavaScript (progressive enhancement
  for live updates only)
- Agent must never transmit sensitive data — reported inventory and telemetry
  contains only names, states, and metrics; never credentials, secrets, config
  file contents, environment variables, process arguments, or any data that could
  expose how a service is configured
- Agent must run directly on the host — never inside a container or VM; container
  deployment of the agent is explicitly unsupported
- Agent communication must be authenticated and encrypted in transit
- Agent host telemetry (CPU, memory, disk, network) is always private — it must
  never appear on a public status page; only monitors explicitly created from
  agent-discovered resources may be published
- Status pages are always public — they must never require a login or password to view
- All monitoring checks must be configurable with custom intervals, timeouts, and
  retry counts

**User roles:**
- Defined by AI.md PARTs 34 and 35 — covers instance admin, org owner, org
  admin, operator, member, and anonymous viewer

**Compatibility requirements:**
- Status page must be compatible with standard RSS/Atom feed readers for incident
  history subscriptions
- Notification webhook format must be compatible with Uptime Kuma's outgoing
  webhook schema to ease migration
- Agent protocol must support future extension without breaking existing deployed
  agents
- The admin dashboard and API must be reachable via the server's Tor hidden
  service (.onion address) when a Tor binary is present on the host
