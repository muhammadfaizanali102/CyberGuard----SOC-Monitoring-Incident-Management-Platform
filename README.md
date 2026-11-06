# CyberGuard -- SOC Monitoring & Incident Management Platform

CyberGuard is a web-based Security Operations Center (SOC) dashboard
designed to help security analysts monitor alerts, investigate
suspicious activity, manage incidents, and review security events from a
centralized interface.

**Live application:** https://cyberguard-dyei40.v2.appdeploy.ai/

> **Project status:** The CyberGuard web application is deployed. The
> dashboard currently supports sample and manually created alerts. Wazuh
> webhook ingestion has been added to the application, but the
> end-to-end connection to the local Wazuh lab still requires secure
> token configuration and testing. Do not describe the integration as
> fully operational until a real Wazuh event appears in the dashboard.

------------------------------------------------------------------------

## Table of Contents

-   [Overview](#overview)
-   [Features](#features)
-   [Dashboard](#dashboard)
-   [Architecture](#architecture)
-   [Wazuh Integration Status](#wazuh-integration-status)
-   [Security Notes](#security-notes)
-   [Technology](#technology)
-   [Getting Started](#getting-started)
-   [Future Improvements](#future-improvements)
-   [Author](#author)

## Overview

CyberGuard provides a centralized view of security alerts and incident
investigations. Its SIEM-inspired dashboard presents alert severity,
recent events, incident activity, and security trends in a dark
interface with blue and emerald accents.

The project is intended as a learning and portfolio project for SOC
monitoring, defensive security, alert triage, and incident response
workflows.

## Features

### SOC Dashboard

-   Critical, High, Medium, and Low alert counters.
-   Alert activity trend visualization.
-   Alert severity distribution.
-   Active security alerts table.
-   Top offense categories.
-   Recent incidents and event timeline.
-   Severity breakdown.

### Alert Management

-   Add security alerts manually.
-   Record alert title, source IP, destination IP, severity,
    description, and timestamp.
-   Search and filter alert records.
-   Review alert details and investigate suspicious activity.

### Incident Management

-   Create incidents from alerts.
-   Track incident states:
    -   Open
    -   Investigating
    -   Resolved
-   Associate MITRE ATT&CK technique references with investigations.
-   Generate downloadable incident investigation reports.

### Wazuh Webhook Integration

The application includes an authenticated ingestion endpoint intended to
receive Wazuh alert JSON:

``` text
POST https://cyberguard-dyei40.v2.appdeploy.ai/api/wazuh/ingest
```

The endpoint expects the shared secret in this HTTP header:

``` text
x-cyberguard-token: YOUR_SECRET_TOKEN
```

The secret is configured in the AppDeploy application's secret settings
using this exact name:

``` text
CYBERGUARD_WAZUH_TOKEN
```

The dashboard also provides a Wazuh integration status and last-event
timestamp. A successful deployment of the endpoint does not, by itself,
mean the local Wazuh server is connected. The n8n-to-CyberGuard delivery
must be configured and verified with a real event.

## Dashboard

Open the deployed application:

**[Launch CyberGuard](https://cyberguard-dyei40.v2.appdeploy.ai/)**

The dashboard is designed to provide an at-a-glance SOC view, while the
alert and incident sections support investigation workflows.

## Architecture

The planned lab integration uses n8n as a secure outbound bridge between
the local VirtualBox lab and the hosted CyberGuard application:

``` text
Kali / Ubuntu / Windows endpoints
              |
              v
       Wazuh Manager
              |
              v
        Wazuh alerts
              |
              v
       n8n workflow
              |
       HTTPS POST + token
              |
              v
     CyberGuard API endpoint
              |
              v
       Alerts database
              |
              v
     CyberGuard SOC dashboard
```

This approach avoids exposing the Wazuh Manager's private VirtualBox
network directly to the public internet.

## Wazuh Integration Status

The integration is **pending end-to-end verification**.

Before using live monitoring:

1.  Generate a strong random token on a trusted machine:

    ``` bash
    openssl rand -hex 32
    ```

2.  Save it as the AppDeploy secret named `CYBERGUARD_WAZUH_TOKEN`. Keep
    the token private.

3.  Configure the n8n HTTP Request node to send a `POST` request to the
    ingestion endpoint shown above.

4.  Add the header `x-cyberguard-token` with the same token.

5.  Forward the Wazuh alert JSON in the request body.

6.  Trigger a harmless test event in the lab and verify that it appears
    in CyberGuard.

7.  Confirm that the dashboard's last-event timestamp updates.

**Important:** Never put the real token in this README, source code,
screenshots, GitHub issues, or public repository files. If a token is
exposed, rotate it immediately.

## Security Notes

-   Do not expose the Wazuh Manager dashboard or API directly to the
    public internet for this integration.
-   Use HTTPS for requests from n8n to CyberGuard.
-   Keep credentials in the hosting platform's secret manager or another
    secure credential store.
-   Do not commit `.env` files, API tokens, passwords, private keys, or
    production event data.
-   Use sanitized sample events when sharing screenshots or
    demonstrations.
-   Only monitor systems and networks for which you have authorization.

## Technology

The deployed project uses a React-based frontend and an AppDeploy-hosted
backend/API with persistent application data. The Wazuh integration is
designed to receive alert events through an authenticated HTTPS webhook,
with n8n serving as the lab-side automation bridge.

> Add exact library versions, database details, and local development
> commands here only after confirming them from the exported project
> source. The public application URL alone does not provide the complete
> source-code bundle or deployment configuration.

## Getting Started

### Use the deployed application

1.  Open [CyberGuard](https://cyberguard-dyei40.v2.appdeploy.ai/).
2.  Review the dashboard and sample events.
3.  Add a manual alert to explore alert management.
4.  Create an incident and update its investigation status.
5.  Generate an investigation report.

### Run or self-host the source

The live URL is a hosted deployment, not a downloadable source-code
archive. To run or self-host the project, first export or obtain the
complete application source from the AppDeploy project. Then follow the
setup instructions and environment-variable requirements included with
that source. Do not assume that cloning this README alone will reproduce
the deployed application.

## Future Improvements

-   Complete and verify live Wazuh-to-n8n-to-CyberGuard ingestion.
-   Add more SIEM integrations, such as Elastic Security or Splunk.
-   Add role-based access control for SOC analysts and administrators.
-   Add alert deduplication, enrichment, and triage workflows.
-   Add configurable alert notifications and response playbooks.
-   Improve audit logging and integration health monitoring.

## Author

**Muhammad Faizan Ali**\
Cyber Security Student \| SOC Monitoring \| Network Security \| Ethical
Hacking

-   GitHub:
    [@muhammadfaizanali102](https://github.com/muhammadfaizanali102)

------------------------------------------------------------------------

If you find this project useful, consider starring the repository.
