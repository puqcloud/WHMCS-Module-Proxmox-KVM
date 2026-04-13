# Change Package

### Proxmox KVM module **[WHMCS](https://puqcloud.com/link.php?id=77)**
#####  [Order now](https://puqcloud.com/whmcs-module-proxmox-kvm.php) | [Download](https://download.puqcloud.com/WHMCS/servers/PUQ_WHMCS-Proxmox-KVM/) | [FAQ](https://faq.puqcloud.com/)

## Overview

When a client upgrades or downgrades their VM package, the module initiates a change package pipeline. Similar to the deploy process, this pipeline uses a state machine to apply the new package settings step by step. The VM is stopped during the process and restarted once all changes are applied.

## Change Package Pipeline Steps

The full change package pipeline consists of the following steps, executed in order:

```
change_package → cp_update_ip → cp_stop → cp_cpu_ram → cp_system_disk_size →
cp_system_disk_bandwidth → cp_additional_disk → cp_additional_disk_size →
cp_additional_disk_bandwidth → cp_network → cp_firewall → cp_start → ready
```

### Step Descriptions

| Step | Description |
|------|-------------|
| `change_package` | Initial state. The new package parameters are loaded and the change process begins. |
| `cp_update_ip` | IP address allocation is updated if the new package requires different IP configuration (e.g., additional IPs or different network). |
| `cp_stop` | The VM is stopped to allow hardware configuration changes. Some changes (disk resize, CPU/RAM on certain configurations) require the VM to be offline. |
| `cp_cpu_ram` | CPU cores, sockets, and RAM are updated to match the new package settings. |
| `cp_system_disk_size` | The system disk is resized if the new package specifies a larger disk. |
| `cp_system_disk_bandwidth` | System disk I/O bandwidth limits are updated. |
| `cp_additional_disk` | Additional disk is created if the new package includes one and the VM does not already have it. |
| `cp_additional_disk_size` | The additional disk is resized to match the new package. |
| `cp_additional_disk_bandwidth` | Additional disk I/O bandwidth limits are updated. |
| `cp_network` | Network settings are updated: rate limit, bridge, VLAN changes. |
| `cp_firewall` | Firewall rules and IPSet are updated if network configuration changed. |
| `cp_start` | The VM is started with the new configuration. |
| `ready` | Final state. The VM is running with the updated package settings. |

## Step Skipping

The change package pipeline is optimized to skip steps where no actual change is needed. For each step, the module compares the current VM configuration with the new package settings. If they are identical, the step is marked as complete immediately and the pipeline advances to the next step.

For example, if the client upgrades from 2 GB RAM to 4 GB RAM but the disk size remains the same, the disk resize steps will be skipped.

This optimization reduces the total time required for package changes and minimizes unnecessary API calls to Proxmox.

## Retry Logic

The change package pipeline uses the same retry logic as the deploy process:

- If a step fails, the status remains at the current step
- The cron retries the step on its next run
- There is no retry limit

![Change package log with retry](../img/cron-change-package-log-retry.png)

## Successful Change Package

When all steps complete successfully, the VM returns to the `ready` state and is running with the new configuration.

![Successful change package log](../img/cron-change-package-log-success.png)

## Triggering a Change Package

A change package is triggered when:

- A client completes an upgrade/downgrade order in the client area
- An admin clicks **Change Package** in the service module commands
- The package change is initiated via the WHMCS API

The `change_package` state is set immediately, and the cron picks up the VM for processing on its next run.

> **Note:** Disk sizes can only be increased, not decreased. If the new package specifies a smaller disk than the current one, the disk resize step will be skipped. This is a Proxmox limitation — virtual disks cannot be shrunk.
