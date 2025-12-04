# Architecture Overview – AWS x Zendesk Integration

## Purpose

This document provides a high-level view of the **AWS + Zendesk integration** used in this case study.  
It is written to be:
- Technically accurate
- Easy to understand for a global audience
- A foundation for both **implementation** and **assessment-style content**

---

## Components

This integration uses the following components:

- **Zendesk**  
  - Sends an HTTP request via a **webhook** when a ticket event occurs.

- **Amazon API Gateway**  
  - Exposes a public HTTPS **endpoint** that Zendesk calls.
  - Validates the request and forwards it to a Lambda function.

- **AWS Lambda**  
  - Receives the event payload from API Gateway.
  - Processes the data (for example: reads requester details or updates fields).
  - Sends an updated payload or API request back to Zendesk as needed.

- **(Optional) Amazon CloudWatch Logs**  
  - Captures logs from Lambda for debugging and auditing.

---

## Data Flow (High Level)

1. A ticket is created or updated in **Zendesk**.  
2. A **Zendesk Trigger** or Automation activates a **Webhook**.  
3. The Webhook sends an HTTP `POST` request to an **Amazon API Gateway** endpoint.  
4. API Gateway forwards the request to an **AWS Lambda function**.  
5. The Lambda function:
   - Parses the JSON payload
   - Applies business logic
   - Optionally calls back to Zendesk’s API to update ticket fields or requester data
6. Lambda logs its actions to **CloudWatch Logs** for debugging and monitoring.

---

## Architectural Diagram (Text-Based)

```text
Zendesk Ticket Event
        │
        ▼
   Zendesk Webhook (HTTPS)
        │
        ▼
 Amazon API Gateway (REST endpoint)
        │
        ▼
    AWS Lambda Function
        │
        ├──> (optional) Zendesk API call (update ticket/requester)
        └──> CloudWatch Logs (for debugging)
