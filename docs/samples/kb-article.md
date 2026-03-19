# Falcon Sensor Not Reporting to Console

| | |
|---|---|
| **Article type** | Troubleshooting / Break-fix |
| **Audience** | IT administrators and security engineers |
| **Product** | CrowdStrike Falcon |

## Overview

If a host with the Falcon sensor installed does not appear in the Falcon console, or shows as inactive, the sensor may not be communicating with the CrowdStrike cloud. This article helps you identify the root cause and restore connectivity.

!!! note
    This article covers Windows hosts. For macOS or Linux, see the platform-specific troubleshooting guides.

## Symptoms

- Host does not appear under **Hosts** in the Falcon console after installation
- Host shows status: **Inactive** or **Last Seen** more than 30 minutes ago
- Sensor service is running locally but console shows no telemetry
- No detections or events are appearing from an expected host

## Common Causes

| Cause | Description |
|---|---|
| **Network / firewall blocking** | Outbound traffic to CrowdStrike cloud endpoints is blocked on port 443 |
| **Proxy misconfiguration** | Sensor is not configured to route traffic through the corporate proxy |
| **Sensor service not running** | The CrowdStrike Falcon Sensor service has stopped or failed to start |
| **Clock skew** | Host system time is out of sync, causing TLS handshake failures |
| **Incorrect Customer ID (CID)** | Sensor was installed with a wrong or invalid CID |

## Resolution Steps

### Step 1 — Verify the sensor service is running

On the affected Windows host, open Command Prompt as Administrator and run:
```cmd
sc query csagent
```

The `STATE` field should show `RUNNING`. If it shows `STOPPED`, start the service:
```cmd
sc start csagent
```

!!! note
    If the service fails to start, check Windows Event Viewer > Application and Services Logs > CrowdStrike for error details.

### Step 2 — Check network connectivity to CrowdStrike cloud

Confirm the host can reach CrowdStrike cloud endpoints on port 443:
```powershell
Test-NetConnection -ComputerName ts01-b.cloudsink.net -Port 443
```

A successful connection returns `TcpTestSucceeded: True`. If this fails, work with your network team to whitelist the required CrowdStrike URLs.

### Step 3 — Validate proxy configuration

If your environment uses a proxy, verify the sensor is configured to use it:

1. Open **Registry Editor** (`regedit.exe`) as Administrator
2. Navigate to: `HKEY_LOCAL_MACHINE\SYSTEM\CrowdStrike\{9b03c1d9-3138-44ed-9fae-d9f4c034b88d}\{16e0423f-7058-48c9-a204-725362b67639}\Default`
3. Check that **ProxyServer** and **ProxyPort** values match your corporate proxy settings
4. Restart the sensor service after making changes:
```cmd
sc stop csagent && sc start csagent
```

### Step 4 — Check system clock

TLS connections require the host clock to be within 5 minutes of UTC:
```cmd
w32tm /query /status
```

If the clock is out of sync, resync it:
```cmd
w32tm /resync /force
```

### Step 5 — Verify the Customer ID (CID)

1. In the Falcon console, go to **Host Setup and Management > Deploy > Sensor Downloads**
2. Copy your CID from the top of the page
3. On the host, open Registry Editor and confirm the `CustomerID` value matches your CID exactly

If it does not match, uninstall and reinstall the sensor using the correct CID.

## Verify Resolution

After completing the steps above, wait 5–10 minutes, then check the Falcon console under **Hosts**. The host should appear with a status of **Normal** and a recent **Last Seen** timestamp.

!!! tip
    If the host still does not appear after 15 minutes, collect sensor diagnostics and contact CrowdStrike Support.

## Related Articles

- How to collect Falcon sensor diagnostics — Windows
- CrowdStrike firewall and proxy requirements
- Uninstalling and reinstalling the Falcon sensor