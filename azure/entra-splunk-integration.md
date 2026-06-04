# Azure/Entra ID → Splunk Integration

Stream Microsoft Entra ID audit logs, sign-in logs, and activity data into Splunk Enterprise using Azure Event Hub as the transport layer.

---

## Overview

This project configures end-to-end telemetry collection from Microsoft Entra ID into Splunk, enabling SIEM-based monitoring of cloud identity activity. The pipeline uses Azure Diagnostic Settings to forward logs to an Event Hub, and the Splunk Add-on for Microsoft Cloud Services to ingest from that Event Hub into a dedicated index.

**Architecture:**

```
Entra ID / Azure Activity
        │
        ▼
  Diagnostic Settings
        │
        ▼
   Azure Event Hub
        │
        ▼
Splunk Add-on for Microsoft Cloud Services
        │
        ▼
   Splunk (index=azure)
```

---

## Prerequisites

- Splunk Enterprise instance (running and accessible)
- Azure subscription with Entra ID access
- Permissions to create Event Hubs, App Registrations, and IAM role assignments in Azure

---

## Step 1 — Install the Splunk Add-on

In Splunk: **Apps → Manage Apps → Browse more apps**

Search for **Splunk Add-on for Microsoft Cloud Services** and install it.

> A free Splunk.com account is required to browse and install apps from Splunkbase.

---

## Step 2 — Create an Azure Event Hub Namespace

1. In the Azure portal, search for and navigate to **Event Hubs**
2. Click **Create**
3. Select your subscription and resource group (or create a new one)
4. Give the namespace a unique name
5. Select your pricing tier
6. Click **Review + Create**, then **Create**

Deployment takes a few minutes.

---

## Step 3 — Create an Event Hub within the Namespace

1. Navigate to your newly created Event Hub namespace
2. Under **Entities**, click **Event Hubs**
3. Click **+ Event Hub**
4. Give it a name (e.g. `condef-logs`)
5. Set retention to **168 hours (7 days)**
6. Click **Review + Create**, then **Create**

---

## Step 4 — Configure Entra ID Diagnostic Settings

1. In the Azure portal, navigate to **Microsoft Entra ID**
2. Under **Monitoring**, click **Diagnostic Settings**
3. Click **Add diagnostic setting**
4. Configure as follows:
   - **Name:** a descriptive name (e.g. `splunk-diag`)
   - **Logs:** select all categories
   - **Destination:** Stream to an event hub → select the Event Hub created above
5. Click **Save**

---

## Step 5 — Create an App Registration (Service Account)

Splunk needs an application identity with permission to read from the Event Hub.

1. In the Azure portal, navigate to **App Registrations**
2. Click **New Registration**
3. Give it a name (e.g. `splunk-collect`), leave all other defaults
4. Click **Register**
5. Note down:
   - **Application (client) ID**
   - **Directory (tenant) ID**

Then create a client secret:

1. Click **Certificates & secrets → New client secret**
2. Add a description and set an appropriate expiry
3. Click **Add**
4. **Copy the secret value immediately** — it won't be shown again

---

## Step 6 — Assign Event Hub Reader Role

Grant the app registration permission to read from the Event Hub:

1. Navigate to **Subscriptions → [your subscription] → Access Control (IAM)**
2. Click **Add → Add role assignment**
3. Search for and select **Azure Event Hubs Data Receiver**
4. Click **Next**
5. Ensure **User, group, or service principal** is selected
6. Click **Select Members**, search for your app registration, select it
7. Click **Review + Assign**

---

## Step 7 — Configure the Splunk Add-on

### Add the Azure App Account

In Splunk, open the **Splunk Add-on for Microsoft Cloud Services**:

1. Go to **Configuration → Azure App Account → Add**
2. Fill in:
   - **Name:** a label for this account (e.g. `splunk-azure`)
   - **Client ID:** Application (client) ID from Step 5
   - **Key (Client Secret):** secret value from Step 5
   - **Tenant ID:** Directory (tenant) ID from Step 5
3. Click **Add**

### Create the Event Hub Input

1. Go to **Inputs → Create New Input → Azure Event Hub**
2. Fill in:
   - **Name:** input label (e.g. `azure-eventhub`)
   - **Azure App Account:** select the account configured above
   - **Event Hub Namespace (FQDN):** found in Azure portal under your Event Hub namespace → Overview
   - **Event Hub Name:** the name of the specific Event Hub created in Step 3
   - **Index:** `azure`
3. Click **Add**

---

## Step 8 — Validate

After 5–10 minutes, run the following search in Splunk to confirm events are flowing:

```
index=azure
```

You should see Entra ID audit and sign-in events appearing in the results.

---

## Notes

- The `azure` index must exist in Splunk before creating the input, or events will be dropped
- Client secret expiry should be tracked — when it expires, ingestion will silently stop
- In a production environment, scope the IAM role assignment to the specific Event Hub namespace rather than the subscription level
