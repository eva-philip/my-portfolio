# Integrating Falcon with Splunk SIEM

| | |
|---|---|
| **Article type** | Integration / How-to guide |
| **Audience** | Security engineers and SOC analysts |
| **Product** | CrowdStrike Falcon + Splunk |

## Overview

This guide walks you through integrating CrowdStrike Falcon with Splunk to stream detection events, endpoint telemetry, and audit logs directly into your Splunk environment. Once configured, Falcon data will appear in Splunk within minutes, enabling correlation with other security data sources in your SOC.

## Before You Begin

| Requirement | Details |
|---|---|
| **Falcon subscription** | Falcon Insight XDR or Falcon Intelligence required |
| **Splunk version** | Splunk Enterprise 8.x or above, or Splunk Cloud |
| **CrowdStrike Add-on for Splunk** | Version 3.x or above (available on Splunkbase) |
| **Falcon API credentials** | Client ID and Client Secret with Event Streams scope |
| **Network access** | Splunk instance must reach api.crowdstrike.com on port 443 |

## Step 1 — Generate Falcon API Credentials

1. Log in to the Falcon console at `falcon.crowdstrike.com`
2. Go to **Support and Resources > API Clients and Keys**
3. Select **Add new API client**
4. Enter a name for the client (for example, `Splunk Integration`)
5. Under **Scopes**, enable the following permissions:
    - Event Streams — Read
    - Detections — Read
    - Hosts — Read
6. Select **Add** to create the client
7. Copy the **Client ID** and **Client Secret**

!!! warning
    You will not be able to view the Client Secret again after closing this window. Store it securely and treat it like a password.

## Step 2 — Install the CrowdStrike Add-on for Splunk

1. In Splunk, go to **Apps > Find More Apps**
2. Search for **CrowdStrike Falcon Event Streams**
3. Select **Install** and follow the prompts
4. Restart Splunk when prompted

Alternatively, download the add-on from Splunkbase and install it manually via **Apps > Manage Apps > Install app from file**.

## Step 3 — Configure the Integration

1. In Splunk, go to **Apps > CrowdStrike Falcon Event Streams > Configuration**
2. Select **Add Account** and enter the following:
    - **Account Name**: A label for this connection (for example, `Production Falcon`)
    - **Client ID**: The Client ID from Step 1
    - **Client Secret**: The Client Secret from Step 1
    - **Base URL**: `https://api.crowdstrike.com`
3. Select **Save**
4. Go to **Inputs > Create New Input**
5. Select the account you just created and configure the data input:
    - **Index**: Select or create a Splunk index for Falcon data (recommended: `crowdstrike`)
    - **Event types**: Select **All** or specific event types such as `DetectionSummaryEvent`
6. Select **Save** to activate the data stream

## Step 4 — Verify the Integration

Allow 5–10 minutes for data to begin flowing, then verify:

1. In Splunk Search, run:
```splunk
index="crowdstrike" | head 10
```

2. You should see Falcon events in the results. If no results appear after 15 minutes, check the Splunk internal logs:
```splunk
index=_internal source=*crowdstrike* error
```

!!! tip
    To view detections specifically, search: `index="crowdstrike" EventType=DetectionSummaryEvent`

## Troubleshooting

| Issue | Resolution |
|---|---|
| **No data in Splunk after 15 minutes** | Check that API credentials are correct and that the Splunk instance can reach api.crowdstrike.com on port 443 |
| **Authentication failed error** | Regenerate the Client Secret in the Falcon console and update the Splunk configuration |
| **Missing event types** | Ensure the API client has the correct scopes enabled |
| **High event volume impacting Splunk** | Filter specific EventType values in the Splunk input configuration instead of streaming all events |