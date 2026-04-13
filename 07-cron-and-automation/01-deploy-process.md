# Deploy Process

### Proxmox KVM module **[WHMCS](https://puqcloud.com/link.php?id=77)**
#####  [Order now](https://puqcloud.com/whmcs-module-proxmox-kvm.php) | [Download](https://download.puqcloud.com/WHMCS/servers/PUQ_WHMCS-Proxmox-KVM/) | [FAQ](https://faq.puqcloud.com/)

## Overview

When a new virtual machine is provisioned (via order, admin action, or API), the module initiates a multi-step deploy pipeline. Each step is processed by the cron system and tracked in the deploy log. The pipeline uses a state machine — the VM progresses through states sequentially, and each state must complete successfully before advancing to the next.

## Deploy Pipeline Steps

The full deploy pipeline consists of the following steps, executed in order:

```
creation → set_ip → clone → migrated → set_cpu_ram → set_system_disk_size →
set_system_disk_bandwidth → set_created_additional_disk → set_additional_disk_size →
set_additional_disk_bandwidth → set_network → set_firewall → set_cloudinit →
starting → ready
```

### Step Descriptions

| Step | Description |
|------|-------------|
| `creation` | Initial state. The VM record is created in the database with all configuration parameters. |
| `set_ip` | An IP address is allocated from the configured IP pool and assigned to the VM record. |
| `clone` | The source template is cloned to create the new VM. Supports both linked clone and full clone modes. The clone is created on the storage specified in the product configuration. |
| `migrated` | If the template and target node differ, the VM is migrated to the correct node. This step handles cross-node deployment when templates reside on a different node than the target. If no migration is needed, this step is completed immediately. |
| `set_cpu_ram` | CPU cores, sockets, and RAM are configured on the VM according to the product/package settings. |
| `set_system_disk_size` | The system (boot) disk is resized to the configured size. |
| `set_system_disk_bandwidth` | Disk I/O bandwidth limits are applied to the system disk (read/write MBps and IOPS). |
| `set_created_additional_disk` | If an additional disk is configured, it is created and attached to the VM. |
| `set_additional_disk_size` | The additional disk is resized to the configured size. |
| `set_additional_disk_bandwidth` | Disk I/O bandwidth limits are applied to the additional disk. |
| `set_network` | Network configuration is applied: bridge, VLAN, network rate limit, and firewall flag on the NIC. |
| `set_firewall` | Proxmox firewall rules are configured, including the anti-spoofing IPSet with the allocated IP addresses. |
| `set_cloudinit` | Cloud-init parameters are set: hostname, IP address, gateway, DNS, username, password, and SSH keys. |
| `starting` | The VM is started. |
| `ready` | Final state. The VM is running and accessible. The "VM is ready" email notification is sent to the client. |

## State Machine and Retry Logic

The deploy pipeline operates as a state machine:

- Each VM has a current `deploy_status` that indicates which step it is on
- The cron task picks up VMs that are not yet in `ready` state and processes the current step
- If a step **succeeds**, the status advances to the next step
- If a step **fails** (e.g., Proxmox API timeout, task not yet complete), the status remains unchanged and the step is retried on the next cron run
- There is no limit on retries — the system will continue attempting the current step until it succeeds

This design ensures that transient failures (network timeouts, Proxmox task queues, storage operations) do not abort the entire deployment. The VM simply waits and retries.

## Migration Step

The `migrated` step deserves special attention. It handles a common scenario in Proxmox clusters:

- The VM template may reside on **Node A** (e.g., because templates are stored on local storage of a specific node)
- The client's VM should be deployed on **Node B**

In this case:

1. The `clone` step creates the VM on Node A (where the template lives)
2. The `migrated` step migrates the cloned VM from Node A to Node B with the correct target storage
3. Subsequent steps configure the VM on its final node

If the template and target node are the same, the migration step completes immediately with no action.

## Deploy Log

Every step is logged with timestamps, status, and any error messages. You can view the deploy log for any VM in the addon's VM Management section.

![Deploy log showing successful deployment](../img/cron-deploy-log-success.png)

## Triggering a Deploy

A deploy is triggered when:

- A new order is provisioned (automatically or manually)
- An admin clicks **Create** in the service module commands
- The service is created via the WHMCS API

The initial `creation` state is set immediately, and the cron picks up the VM for processing on its next run.
