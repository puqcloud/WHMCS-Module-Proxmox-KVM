# Email Templates

### Proxmox KVM module **[WHMCS](https://puqcloud.com/link.php?id=77)**
#####  [Order now](https://puqcloud.com/whmcs-module-proxmox-kvm.php) | [Download](https://download.puqcloud.com/WHMCS/servers/PUQ_WHMCS-Proxmox-KVM/) | [FAQ](https://faq.puqcloud.com/)

## Overview

The module uses five email templates to notify clients about VM lifecycle events. These templates must be created manually in WHMCS before the module can send the corresponding notifications.

## Creating Email Templates in WHMCS

1. Navigate to **Setup > Email Templates** in the WHMCS admin area
2. Click **Create New Email Template**
3. Set the **Type** to **Product/Service**
4. Set the **Unique Name** to the exact template name listed below
5. Fill in the subject line and email body using the available variables
6. Click **Save Changes**

Repeat for all five templates.

> **Important:** The template unique names must match exactly as listed below. The module looks up templates by their unique name, so any mismatch will prevent the email from being sent.

## Email Templates

### 1. puqProxmoxVKM Welcome Email

> **Note the spelling.** The original template name is literally `puqProxmoxVKM Welcome Email` (with `VKM`, not `KVM`). It has stayed like this since v1.0 for backwards compatibility — if you rename it the module will stop sending the welcome email.

Sent to the client as an order confirmation when a new Proxmox KVM service is created. At this point the VM is not yet ready — this email just tells the client their order has been accepted.

**When sent:** Upon service creation / order acceptance, before the deploy pipeline runs.

**Unique Name:** `puqProxmoxVKM Welcome Email`
**Email Type:** Product/Service
**Subject:** `Virtual Machine Order Information`

**Body:**

```
Dear {$client_name},

Your order has been accepted for implementation.
Installing and pre-configuring the virtual machine will take some time.
Please wait for an e-mail with information that the virtual machine is ready for use, also with access parameters.

Product/Service: {$service_product_name}
Payment Method: {$service_payment_method}
Amount: {$service_recurring_amount}
Billing Cycle: {$service_billing_cycle}
Next Due Date: {$service_next_due_date}

Important note - if you have also purchased the backup options, do not forget to configure the schedule in the service's subpage.

Thank you for choosing us.

{$signature}
```

---

### 2. puqProxmoxKVM VM is ready

Sent to the client when a new virtual machine has been successfully deployed and is ready for use. **This is the most important template** — it contains the VM access credentials (IP, username, password).

**When sent:** After the deploy pipeline reaches the `ready` state and the VM is confirmed running.

**Unique Name:** `puqProxmoxKVM VM is ready`
**Email Type:** Product/Service
**Subject:** `Virtual Machine is ready`

**Body:**

```
Dear {$client_name},

Your virtual machine is already ready.
You can connect to it using data.

Product/Service: {$service_product_name}
Payment Method: {$service_payment_method}
Amount: {$service_recurring_amount}
Billing Cycle: {$service_billing_cycle}
Next Due Date: {$service_next_due_date}

IP address: {$service_dedicated_ip} or {$service_domain}
Username: {$service_username}
Password: {$service_password}

Thank you for choosing us.

{$signature}
```

---

### 3. puqProxmoxKVM Reset password

Sent to the client after a password reset operation on their virtual machine.

**When sent:** After a password reset is performed via the admin area or client area — once cloud-init has re-applied the new credentials and the VM has been restarted.

**Unique Name:** `puqProxmoxKVM Reset password`
**Email Type:** Product/Service
**Subject:** `Reset password is ready`

**Body:**

```
Dear {$client_name},

Password reset successful.

IP address: {$service_dedicated_ip} or {$service_domain}
Username: {$service_username}
Password: {$service_password}

Thank you for choosing us.

{$signature}
```

---

### 4. puqProxmoxKVM Backup restored

Sent to the client after a backup has been successfully restored.

**When sent:** After a backup restore operation completes and the module has re-applied CPU/RAM/disk/network settings and restarted the VM.

**Unique Name:** `puqProxmoxKVM Backup restored`
**Email Type:** Product/Service
**Subject:** `Backup restored successful`

**Body:**

```
Dear {$client_name},

Backup restored successful.

Product/Service: {$service_product_name}
Payment Method: {$service_payment_method}
Amount: {$service_recurring_amount}
Billing Cycle: {$service_billing_cycle}
Next Due Date: {$service_next_due_date}

IP address: {$service_dedicated_ip} or {$service_domain}

Thank you for choosing us.

{$signature}
```

---

### 5. puqProxmoxKVM Upgrade Email

Sent to the client after a package upgrade or downgrade completes.

**When sent:** After the `change_package` state machine finishes applying the new product parameters to the VM and starts it back up.

**Unique Name:** `puqProxmoxKVM Upgrade Email`
**Email Type:** Product/Service
**Subject:** `Virtual Machine upgrade is ready`

**Body:**

```
Dear {$client_name},

Virtual Machine upgrade is successful.

Product/Service: {$service_product_name}
Payment Method: {$service_payment_method}
Amount: {$service_recurring_amount}
Billing Cycle: {$service_billing_cycle}
Next Due Date: {$service_next_due_date}

IP address: {$service_dedicated_ip} or {$service_domain}

Thank you for choosing us.

{$signature}
```

> The bodies above are the original PUQcloud templates shipped with v1.0 through v3.0. You are free to customize subjects and bodies to match your brand — only the **Unique Name** and **Email Type** must stay as documented, because the module looks up templates by their unique name.

## Available Template Variables

The following merge fields are available in all email templates:

### Standard WHMCS Variables

| Variable | Description |
|----------|-------------|
| `{$client_name}` | Client's full name |
| `{$service_product_name}` | Product/service name |
| `{$service_dedicated_ip}` | Primary IPv4 address assigned to the VM |
| `{$service_domain}` | Service domain / hostname |
| `{$service_username}` | Operating system username |
| `{$service_password}` | Operating system password |
| `{$service_recurring_amount}` | Recurring billing amount |
| `{$service_billing_cycle}` | Billing cycle (Monthly, Quarterly, etc.) |
| `{$service_next_due_date}` | Next payment due date |
| `{$signature}` | Email signature configured in WHMCS |

### Module-Specific Variables

| Variable | Description |
|----------|-------------|
| `{$service_assigned_ips}` | All assigned IP addresses |
| `{$vm_id}` | Proxmox virtual machine ID (VMID) |
| `{$server_hostname}` | Proxmox server hostname |

In addition, all standard WHMCS client and service merge fields are available.
