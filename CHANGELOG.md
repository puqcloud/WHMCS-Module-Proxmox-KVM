# Changelog

### Proxmox KVM module **[WHMCS](https://puqcloud.com/link.php?id=77)**
#####  [Order now](https://puqcloud.com/whmcs-module-proxmox-kvm.php) | [Download](https://download.puqcloud.com/WHMCS/servers/PUQ_WHMCS-Proxmox-KVM/) | [FAQ](https://faq.puqcloud.com/)

---

## v3.0 (April 2026) — Major Release

Version 3.0 is a complete rewrite with a new architecture, dedicated addon module, and dozens of new features. This is the biggest update since the initial release.

### New Dedicated Addon Module

The PUQ Customization addon module is **no longer required**. Version 3.0 includes its own dedicated addon module (`puq_proxmox_kvm`) with:

- **Dashboard** — centralized overview of all resources: IP pools, DNS zones, KVM services
- **IP Pool Management** — redesigned with per-server pools, usage visualization, improved validation
- **DNS Zone Management** — Cloudflare and HestiaCP integration for automatic forward/reverse DNS
- **VM Management** — centralized view of all VMs across all servers with deploy logs, status monitoring, retry/reset actions, and database record inspection
- **Settings** — multi-page settings (General + Cron) with API timeouts, migration configuration, and per-task cron intervals
- **Auto-migration** — seamless migration from old `puq_customization` tables on first activation
- **Access control** — configurable admin role groups for addon access

### Deploy State Machine

The VM deployment process has been completely rewritten as a **step-by-step state machine**:

- Each deployment step is executed individually with status tracking
- If any step fails, deployment pauses and **resumes automatically** on the next cron run
- Full deploy log with per-step timing, status transitions, and error messages
- Visible in both CLI output and admin UI

**Deploy steps:** Allocate IP → DNS + Clone → Migrate to target node → Set CPU & RAM → Resize system disk → Set disk I/O → Create additional disk → Resize additional disk → Set additional disk I/O → Configure network → Configure firewall → Configure cloud-init → Start VM → Verify running + Email

### Post-Clone VM Migration

New intelligent migration system for cross-node deployment:

- After cloning to the template node, VMs are **automatically migrated** to the target node with the correct storage
- Supports offline migration with storage mapping (`targetstorage` parameter)
- Finds suitable target nodes based on storage availability and free RAM
- Configurable migration timeout with cron-based retry
- If migration fails or no suitable node is found, VM stays on the template node and deployment continues

### Change Package State Machine

Package upgrades/downgrades have been rewritten with the same state machine approach:

- **12 individual steps**: Update IP/DNS → Stop VM → Set CPU/RAM → Resize disks → Configure I/O → Configure network → Configure firewall → Start VM → Verify running
- Each step checks if a change is actually needed and **skips if no change** detected
- Full logging to `vm_last_action_log` with step-by-step detail
- Resilient to failures — continues from last successful step on next cron run

### Firewall Management

Complete firewall feature — both for deployment and client self-service:

- **Deploy configuration** — firewall options (enable, DHCP, NDP, MAC filter, IP filter, log levels), policies (input/output), and anti-spoofing IPSet are configured during deployment
- **Client area** — full firewall rules management page: add rules, delete rules, drag-and-drop reorder, change input/output policies
- **Admin product settings** — new "Firewall" panel with all options configurable per product
- **Rule validation** — server-side validation of action, direction, protocol, IP/CIDR, port ranges

### Cron System

Flexible cron with two modes:

- **WHMCS Hook mode** (default) — runs automatically with WHMCS cron
- **Standalone mode** — independent cron file with CLI tools (`--task`, `--force`, `--list`, `--help`)
- **Per-task intervals** — each cron task has its own configurable interval (set to 0 to disable)
- **Lock management** — flock-based locking with stale PID detection and configurable timeout
- **Structured results** — each cron function returns `{processed, errors, details}` for monitoring

### Client Area Redesign

All client area pages have been fully redesigned:

- **AJAX-based** — no Proxmox API calls on page load, all data loaded asynchronously
- **Modern UI** — consistent card-based design with PUQ styling
- **Session cache** — 30-second cache for VM status to reduce API load
- **Fast poll** — 1-second status polling after start/stop for instant feedback
- **New Firewall page** — rules management with drag reorder, policies, add/delete
- **Network info message** — notification when additional IPs need manual configuration
- **Translation support** — all UI text through `L()` helper, 25 languages

### Admin Area Improvements

- **Custom product settings UI** — Bootstrap-based panels replacing default WHMCS configoptions for VM Configuration, Storage, Network, Firewall, Integrations, Email Templates, Client Permissions
- **Real-time VM information** — JSON AJAX panel with status, CPU, RAM, disk, network details
- **Deploy log viewer** — expandable step-by-step deploy history with timing
- **Change package log** — step-by-step change history with skip indicators
- **noVNC + Redeploy buttons** — quick actions in admin service view
- **Metric billing** — bandwidth usage in/out with WHMCS Metric Billing integration

### Security & Stability

- **Path traversal fix** — whitelist validation for addon page routing
- **Admin session check** — explicit `$_SESSION['adminid']` verification in addon AJAX
- **Input validation** — firewall rules, IP/CIDR, port ranges validated server-side
- **Error handling** — safe `explode()` operations, operator precedence fixes, try-catch on database operations
- **DNS log filtering** — sensitive tokens/passwords removed from log output
- **PHP 7.4 compatibility** — `str_contains()` replaced with `strpos()`, no PHP 8.0+ features required

### Compatibility

| Component | Supported Versions |
|-----------|---------------|
| **WHMCS** | 8.x, 9.x      |
| **PHP** | 7.4, 8.1, 8.2 |
| **Proxmox VE** | 8.x, 9.x      |
| **ionCube Loader** | v13, v14, v15 |

---

## v2.4 — 31-08-2025

- Accounted for a custom path to the WHMCS admin panel.
- Direct links now take the `WHMCS System URL` parameter into consideration.

## v2.3 — 09-08-2025

- **Breaking change:** authentication switched from login/password to **Proxmox API token**.
  Users who update **must** create a token and enter the new credentials in the Proxmox server settings.
  Username must be in the format `root@pam!whmcs-dev` (token ID), password — the token value itself.
- Renamed the anti-spoofing rule filter from `wm-VMID` to `ipfilter-net0`.
- Various performance improvements that increased the module's response speed.

> **Warning:** before updating to v2.3+, create a Proxmox API token and enter its details in the server settings.

## v2.2 — 14-07-2025

- Backup restoration mechanism improved.
- Security fixes implemented.
- Client web interface updated: button-related bugs fixed, loaders added.
- Adapted for compatibility with Proxmox v8.4.

## v2.0 — 23-09-2024

- Module is now coded with **ionCube v13**.
- Supported PHP versions:
  - PHP 7.4 — WHMCS ≤ 8.11.0
  - PHP 8.1 — WHMCS ≥ 8.11.0
  - PHP 8.2 — WHMCS ≥ 8.11.0
- Added an active check for PUQ Customization + extension `Module PuqProxmoxKVM`.

## v1.5 — 04-03-2024

- Fixed an `No IPv6 addresses available` bug that occurred during IPv6 assignment in some cases.
- Fixes in client-zone templates.
- Changed the display of the product in the admin area.
- Added metrics for incoming and outgoing traffic (usage-based billing now possible).

## v1.4.5 — 11-10-2023

- Added support for WHMCS 8.8.0.
- Translations added/updated for 25 languages: Arabic, Azerbaijani, Catalan, Chinese, Croatian, Czech, Danish, Dutch, English, Estonian, Farsi, French, German, Hebrew, Hungarian, Italian, Macedonian, Norwegian, Polish, Romanian, Russian, Spanish, Swedish, Turkish, Ukrainian.

## v1.4 — 24-07-2023

- Added synchronization of forward and reverse DNS zones (required PUQ Customization): **Cloudflare**, **HestiaCP**.
- The "Change Package" function moved to cron.
- Fixed a bug related to the default operating system template.
- Added Virtual Machine Templates (CentOS 9).

## v1.3 — 11-07-2023

- Integration with **PUQ Customization** (FREE addon).
- **IPv6** support (required PUQ Customization).
- Ability to create VMs with IPv6 only.
- Added **pools of IP addresses** (required PUQ Customization).
- Added ability to define multiple IPv4 and IPv6 addresses.
- Added configurable options for RAM, CPU, IPv4, IPv6.
- Added a check that re-runs cloning if the previous cloning attempt failed.
- Redesigned the main screen of the client area (dropdown list with VM network settings).
- Changed the display of VM graphs in the admin area (3 graphs per row).
- Removed the "name servers" fields from the order form.
- Added VM templates (Debian 12, Ubuntu 22.04).

## v1.2.1 — 04-03-2023

- Support for PHP 8.1 and PHP 7.4.
- Changes made to templates.

## v1.2 — 06-01-2023

- Support for WHMCS 8.6.
- Support for ionCube PHP Loader v12.
- Support for PHP 8.1.
- Changes made to templates.

## v1.1 — 12-10-2022

- Modified security.
- Remote debug logging in the admin panel.
- Corrections in translations.
- Fixed a bug that incorrectly checked whether a service belongs to the logged-in client in the client area.

## v1.0 — 19-09-2022

First public release.
