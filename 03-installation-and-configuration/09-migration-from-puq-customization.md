# Migration from PUQ Customization

## Overview

Version 3.0 introduces a standalone addon module (`puq_proxmox_kvm`) that replaces the PUQ Customization extension (`ModulePuqProxmoxKVM`).

## Migration Steps

### 1. Install New Modules

Upload both modules to your WHMCS installation:
- `modules/servers/puqProxmoxKVM/` (updated server module v3.0)
- `modules/addons/puq_proxmox_kvm/` (new addon module)

### 2. Activate Addon

1. Go to **Setup > Addon Modules**
2. Activate **PUQ Proxmox KVM**
3. Enter your license key

### 3. Automatic Data Migration

On activation, the addon automatically:
- Creates new database tables (`puq_proxmox_kvm_ip_pools`, `puq_proxmox_kvm_dns_zones`)
- Detects old tables (`puq_customization_module_puq_proxmox_kvm_ip_pools`, `puq_customization_module_puq_proxmox_kvm_dns_zones`)
- Copies all data from old tables to new tables (if new tables are empty)

### 4. Verify Migration

1. Open **Addons > PUQ Proxmox KVM**
2. Check that all IP Pools are present
3. Check that all DNS Zones are present
4. Verify Services Summary shows correct data

### 5. Deactivate Old Extension

Once verified:
1. Go to PUQ Customization addon
2. Deactivate the ModulePuqProxmoxKVM extension

> **Important:** The server module v3.0 supports both the new and old addon simultaneously, so services will continue working during migration.

## Backward Compatibility

The updated server module (v3.0) uses a dual-path approach:
1. First checks for the new standalone addon (`puq_proxmox_kvm`)
2. Falls back to the old PUQ Customization extension if new addon is not found

This ensures zero downtime during the migration process.
