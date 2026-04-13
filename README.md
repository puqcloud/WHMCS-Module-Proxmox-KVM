# Description

### Proxmox KVM module **[WHMCS](https://puqcloud.com/link.php?id=77)**
#####  [Order now](https://puqcloud.com/whmcs-module-proxmox-kvm.php) | [Download](https://download.puqcloud.com/WHMCS/servers/PUQ_WHMCS-Proxmox-KVM/) | [FAQ](https://faq.puqcloud.com/)

## Proxmox KVM WHMCS Module

The PUQ Proxmox KVM module for WHMCS provides automated provisioning, management, and billing of KVM virtual machines on Proxmox VE clusters. The module consists of two parts: the **Server Module** that handles VM provisioning and the client/admin interface, and the **Addon Module** that manages IP address pools, DNS zones, cron tasks, and provides a centralized management dashboard.

The module allows your customers to provision and manage KVM machines on your Proxmox server or Proxmox cluster. It exposes virtually all functions of Proxmox directly from the WHMCS panel without forcing the user (or admin) to log in to Proxmox itself. This greatly simplifies customer account management, improves customer satisfaction and reduces the number of support requests.

The module is intended for advanced users — installation and correct configuration require knowledge and experience in server and network administration. Although the documentation is detailed and allows the module to be installed by an intermediate user, we strongly suggest following the order defined in the installation chapter.

> **Changed in v3.0.** Starting from v3.0 the module ships its own dedicated addon module (`puq_proxmox_kvm`) — the separate **PUQ Customization** addon required for v1.3–v2.x is **no longer needed**. On first activation the new addon automatically migrates IP pools, DNS zones and VM records from the old `puq_customization` tables.

### Installation service

If you don't feel comfortable performing the installation yourself, PUQcloud offers an installation service in two variants — **module installation and configuration** and **full implementation**. See [puqcloud.com](https://puqcloud.com/whmcs-module-proxmox-kvm.php) for details.

---

## Main Features

- **Automated VM provisioning** — automatic deployment of KVM virtual machines via linked clone or full clone with state machine-based deploy pipeline
- **Post-clone migration** — automatic VM migration to the target node with correct storage after cloning, supporting cross-node deployment with local storage
- **VM lifecycle management** — create, suspend, unsuspend, terminate, reinstall, change package (upgrade/downgrade) with step-by-step state machine and retry logic
- **Firewall management** — configurable firewall policies, anti-spoofing IPSet rules, and client-side firewall rule management (add, delete, reorder)
- **Snapshot management** — create, rollback, and remove snapshots with configurable lifetime and automatic cleanup
- **Backup management** — manual and scheduled backups with restore capability, per-day schedule configuration
- **IPv4/IPv6 IP address pool management** — centralized IP allocation with per-server pools, automatic bridge/VLAN selection
- **DNS zone management** — Cloudflare and HestiaCP integration for forward and reverse DNS automation
- **noVNC web console** — secure browser-based console access via VNC proxy with one-time authentication links
- **Cloud-init support** — automatic hostname, IP, DNS, user, and password configuration via cloud-init
- **ISO mounting** — mount and unmount ISO images from Proxmox storage for OS installation
- **Resource usage charts** — real-time CPU, RAM, disk I/O, and network usage graphs with historical data
- **Usage-based billing** — network traffic metering (inbound/outbound) with WHMCS Metric Billing
- **Configurable client permissions** — per-product control over which features are available to customers (start, stop, noVNC, charts, reinstall, reset password, revDNS, ISO mount, firewall)
- **Multi-language support** — 25 languages including Arabic, Azerbaijani, Catalan, Chinese, Croatian, Czech, Danish, Dutch, English, Estonian, Farsi, French, German, Hebrew, Hungarian, Italian, Macedonian, Norwegian, Polish, Romanian, Russian, Spanish, Swedish, Turkish, and Ukrainian
- **Cron system** — flexible cron with two modes (WHMCS hook or standalone), configurable intervals per task, CLI tools, lock management
- **Admin product settings UI** — custom Bootstrap-based settings panel for VM configuration, storage, network, firewall, integrations, email templates, and client permissions

---

## System Requirements

| Requirement | Minimum          |
|-------------|------------------|
| WHMCS | 8.x or higher    |
| PHP | 7.4, 8.1, or 8.2 |
| Proxmox VE | 8.x or higher    |
| ionCube Loader | v13 or newer     |

---

## Module Components

| Component | Type | Directory |
|-----------|------|-----------|
| Server Module | `puqProxmoxKVM` | `modules/servers/puqProxmoxKVM/` |
| Addon Module | `puq_proxmox_kvm` | `modules/addons/puq_proxmox_kvm/` |

The **Addon Module** is required for the server module to function. It manages:
- IP address pools (IPv4 and IPv6)
- DNS zones (Cloudflare, HestiaCP)
- VM management dashboard
- Cron task orchestration
- Global settings (API timeouts, migration, cron intervals)

---

## Links

- **Product page:** [https://puqcloud.com/whmcs-module-proxmox-kvm.php](https://puqcloud.com/whmcs-module-proxmox-kvm.php)
- **Documentation:** [https://doc.puq.info/books/proxmoxkvm-whmcs-module](https://doc.puq.info/books/proxmoxkvm-whmcs-module)
- **GitHub:** [https://github.com/puqcloud/WHMCS-Module-Proxmox-KVM](https://github.com/puqcloud/WHMCS-Module-Proxmox-KVM)
- **Support:** [https://puqcloud.com/submitticket.php](https://puqcloud.com/submitticket.php?step=2&deptid=1)
- **Community:** [https://community.puqcloud.com/](https://community.puqcloud.com/)

---

## Screenshots

### Client Area — VM Overview

![Client area overview](img/client-area-manage-overview.png)

### Client Area — Firewall Rules

![Client area firewall](img/client-area-firewall-rules.png)

### Admin Area — Product Configuration

![Admin product config](img/admin-product-config-full.png)

### Addon Module — Dashboard

![Addon dashboard](img/addon-dashboard-home.png)
