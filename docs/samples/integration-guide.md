# Integrating Falcon with Splunk SIEM

| About this guide | |
|---|---|
| **Article type** | Integration guide |
| **Audience** | Security engineers and SOC analysts |
| **Product** | CrowdStrike Falcon + Splunk |

This guide walks you through integrating CrowdStrike Falcon with Splunk to stream detection events, endpoint telemetry, and audit logs into your Splunk environment. Once configured, Falcon data flows into Splunk within minutes, enabling correlation with other security data sources in your SOC.

---

## How the integration works

Falcon streams event data to Splunk using the CrowdStrike Falcon Event Streams API. The CrowdStrike Add-on for Splunk acts as a connector, authenticating with the Falcon API using OAuth 2.0 credentials and pulling events into a dedicated Splunk index.

**Data flow:**

```
CrowdStrike Falcon Cloud
        |
        | (OAuth 2.0 / HTTPS / Port 443)
        |
CrowdStrike Add-on for Splunk
        |
        | (Splunk HTTP Event Collector)
        |
Splunk Index (crowdstrike)
        |
        | (SPL queries, dashboards, alerts)
        |
SOC Analyst Workstation
```

Event types streamed include detection summaries, authentication events, endpoint activity, and audit logs. You can filter for specific event types during configuration to control data volume.

---

## Before you begin

Confirm the following requirements before starting.

| Requirement | Details |
|---|---|
| **Falcon subscription** | Falcon Insight XDR or Falcon Intelligence |
| **Splunk version** | Splunk Enterprise 8.x or above, or Splunk Cloud |
| **CrowdStrike Add-on for Splunk** | Version 3.x or above (available on Splunkbase) |
| **Falcon API credentials** | Client ID and Client Secret with Event Streams scope |
| **Network access** | Splunk instance must reach `api.crowdstrike.com` on port 443 outbound |
| **Splunk permissions** | Admin access to install apps and create indexes |

!!! note
    If your Splunk instance sits behind a proxy or firewall, work with your network team to whitelist `api.crowdstrike.com` on port 443 before starting. The integration will not function without this outbound access.

---

## Step 1

### Generate Falcon API credentials

The CrowdStrike Add-on authenticates with Falcon using a dedicated API client. Create a scoped API client specifically for this integration rather than reusing existing credentials.

1. Log in to the Falcon console at `falcon.crowdstrike.com`.
2. Go to **Support and Resources > API Clients and Keys**.
3. Select **Add new API client**.
4. Enter a descriptive name for the client. For example: `Splunk-Integration-Prod`.
5. Under **Scopes**, enable the following permissions:

    | Scope | Permission |
    |---|---|
    | Event Streams | Read |
    | Detections | Read |
    | Hosts | Read |

6. Select **Add** to create the client.
7. Copy the **Client ID** and **Client Secret** and store them securely.

!!! warning
    The Client Secret is shown only once. If you close the window without copying it, you must regenerate it. Store it in a secrets manager or password vault — never in plain text.

---

## Step 2

### Install the CrowdStrike Add-on for Splunk

1. In Splunk, go to **Apps > Find More Apps**.
2. Search for **CrowdStrike Falcon Event Streams**.
3. Select **Install** and follow the prompts.
4. Restart Splunk when prompted.

If your Splunk instance does not have internet access, install the add-on manually:

1. Download the add-on package from [Splunkbase](https://splunkbase.splunk.com).
2. In Splunk, go to **Apps > Manage Apps > Install app from file**.
3. Upload the downloaded package and restart Splunk.

---

## Step 3

### Configure the integration

1. In Splunk, go to **Apps > CrowdStrike Falcon Event Streams > Configuration**.
2. Select **Add Account** and enter the following:

    | Field | Value |
    |---|---|
    | **Account Name** | A label for this connection. For example: `Production Falcon` |
    | **Client ID** | The Client ID from Step 1 |
    | **Client Secret** | The Client Secret from Step 1 |
    | **Base URL** | `https://api.crowdstrike.com` |

3. Select **Save**.
4. Go to **Inputs > Create New Input**.
5. Select the account you created and configure the data input:

    | Field | Recommended value |
    |---|---|
    | **Index** | `crowdstrike` (create this index if it does not exist) |
    | **Event types** | Start with `DetectionSummaryEvent`. Add others as needed. |
    | **Interval** | `60` (seconds between polling requests) |

6. Select **Save** to activate the data stream.

!!! tip
    Start with `DetectionSummaryEvent` only and expand to additional event types once you have confirmed the integration is working. Streaming all event types on a large deployment can generate significant data volume and affect Splunk licensing costs.

---

## Step 4

### Verify the integration

Allow 5 to 10 minutes for data to begin flowing, then run the following queries in Splunk Search.

**Confirm data is arriving:**

```splunk
index="crowdstrike" | head 10
```

**Check for detection events specifically:**

```splunk
index="crowdstrike" EventType=DetectionSummaryEvent
| table _time, ComputerName, DetectName, Severity, Status
| sort -_time
```

**Count events by type to confirm all streams are active:**

```splunk
index="crowdstrike"
| stats count by EventType
| sort -count
```

**Check event volume over the last 24 hours:**

```splunk
index="crowdstrike"
| timechart span=1h count by EventType
```

If no results appear after 15 minutes, check the Splunk internal logs for errors:

```splunk
index=_internal source=*crowdstrike* (error OR warning)
| table _time, message
| sort -_time
```

---

## Security considerations

**Use a dedicated, scoped API client.**
Create a separate API client for each integration rather than sharing credentials across systems. Scope it to only the permissions the integration needs. If the credentials are compromised, you can revoke them without affecting other integrations.

**Store credentials in a secrets manager.**
Never store the Client ID or Client Secret in plain text configuration files, environment variables, or source control. Use a secrets manager such as HashiCorp Vault, AWS Secrets Manager, or CyberArk.

**Restrict network access.**
Lock down outbound traffic from your Splunk instance to `api.crowdstrike.com` on port 443 only. Do not open broad outbound internet access.

**Rotate credentials regularly.**
Rotate the Client Secret every 90 days or immediately if you suspect a compromise. After regenerating the secret in the Falcon console, update the Splunk configuration under **Apps > CrowdStrike Falcon Event Streams > Configuration**.

**Monitor API client activity.**
Review API client usage in the Falcon console under **Support and Resources > API Clients and Keys** periodically to detect unexpected access patterns.

---

## Best practices

**Index Falcon data separately.**
Use a dedicated `crowdstrike` index rather than the default index. This makes it easier to apply retention policies, control access with role-based permissions, and estimate licensing costs.

**Filter event types to control data volume.**
Not all event types are equally useful for every SOC. Start with `DetectionSummaryEvent` and `AuthActivityAuditEvent`, then add others based on your use case. Streaming all events on a large deployment can significantly impact Splunk licensing.

**Set up summary indexes for high-volume queries.**
If you run frequent detection dashboards, use Splunk summary indexing to pre-aggregate data and reduce query times.

**Create a service account for the integration.**
Run the integration under a dedicated Splunk service account rather than a personal admin account. This makes it easier to audit activity and maintain access if team members change.

**Test in a non-production environment first.**
Before configuring the integration in production, validate it in a staging environment to confirm data volume, event types, and field mappings meet your expectations.

---

## Troubleshooting

| Issue | Cause | Resolution |
|---|---|---|
| No data in Splunk after 15 minutes | API credentials are incorrect, or the Splunk instance cannot reach `api.crowdstrike.com` on port 443 | Verify credentials in **Configuration > Accounts**. Run a connectivity test: `curl -v https://api.crowdstrike.com` from the Splunk host. |
| Authentication failed error | The Client Secret has expired or was regenerated | Regenerate the Client Secret in the Falcon console and update it in the Splunk add-on configuration. |
| Missing event types in results | The API client does not have the required scopes | In the Falcon console, edit the API client and confirm the correct scopes are enabled for the missing event types. |
| High event volume impacting Splunk | All event types are enabled on a large deployment | Edit the data input and specify only the event types you need instead of selecting **All**. |
| Duplicate events appearing in Splunk | Multiple data inputs are configured for the same account | Review your inputs under **Inputs > Data Inputs** and remove any duplicates. |
| Add-on not appearing in Splunk after install | Splunk was not restarted after installation | Restart Splunk and check **Apps > Manage Apps** to confirm the add-on is enabled. |
| `api.crowdstrike.com` unreachable from Splunk host | Firewall or proxy is blocking outbound traffic on port 443 | Work with your network team to whitelist `api.crowdstrike.com` on port 443. If using a proxy, configure proxy settings in the add-on under **Configuration > Proxy**. |

---

## Remove the integration

To disable the integration without uninstalling the add-on:

1. In Splunk, go to **Apps > CrowdStrike Falcon Event Streams > Inputs**.
2. Select the data input and set its status to **Disabled**.

To fully remove the integration:

1. Disable and delete all data inputs.
2. Go to **Apps > Manage Apps**, find the CrowdStrike add-on, and select **Delete**.
3. In the Falcon console, go to **Support and Resources > API Clients and Keys** and delete the API client created for this integration.
4. Optionally, delete the `crowdstrike` Splunk index if the data is no longer needed.

!!! warning
    Deleting the Splunk index permanently removes all ingested Falcon data. Confirm with your team before proceeding.

---

## Related resources

- [CrowdStrike Falcon API documentation](https://falcon.crowdstrike.com/documentation)
- [CrowdStrike Add-on for Splunk on Splunkbase](https://splunkbase.splunk.com)
- [Falcon Query Language (FQL) reference](https://falcon.crowdstrike.com/documentation/45/falcon-query-language-fql)
- [Splunk index management documentation](https://docs.splunk.com/Documentation/Splunk/latest/Indexer/Setupmultipleindexes)
