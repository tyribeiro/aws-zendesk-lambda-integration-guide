# Testing and Troubleshooting – AWS x Zendesk Integration

This document describes how to verify the integration works as expected and how to troubleshoot the most common issues.

---

## Basic Test Workflow

1. **Create a test ticket** in Zendesk that meets the Trigger conditions.
2. Confirm that:
   - The **Trigger** fires.
   - The **Webhook** is invoked.
3. In AWS:
   - Check **CloudWatch Logs** for the Lambda function.
   - Confirm that the event payload is present.
4. Validate that any **intended side effects** occur:
   - Ticket fields updated
   - Comment added
   - External system called (if applicable)

---

## Common Issues and Resolutions

### 1. HTTP 404 from Zendesk Webhook

**Symptoms:**
- Zendesk webhook reports `404 Not Found`.
- No logs appear in Lambda for the test event.

**Likely causes:**
- Incorrect **API Gateway Invoke URL**.
- The API was not **deployed** or the wrong **stage** URL was used.

**Resolution:**
- Reopen API Gateway and confirm:
  - The API is deployed to the correct stage (for example, `prod`).
  - The URL copied into Zendesk matches the **deployed** endpoint, including the stage segment.
- Update the webhook URL in Zendesk and test again.

---

### 2. Lambda Not Invoked

**Symptoms:**
- Webhook reports success (2xx), but Lambda logs do not show any new invocations.

**Likely causes:**
- API method is not properly integrated with the Lambda function.
- Wrong Lambda function is selected.

**Resolution:**
- In API Gateway, open the `POST` method for your resource.
- Confirm that:
  - Integration type is set to **Lambda Function**.
  - The correct function is selected.
- Redeploy the API if changes were made and retest.

---

### 3. JSON Parsing Errors in Lambda

**Symptoms:**
- Lambda logs show JSON parsing errors or `KeyError`/`TypeError` issues.
- Integration works intermittently or breaks on some tickets.

**Likely causes:**
- The JSON body sent from Zendesk does not match the structure expected by the Lambda code.
- Certain ticket fields are missing or optional.

**Resolution:**
- Log the raw incoming event payload in Lambda for troubleshooting.
- Update the Lambda code to:
  - Check for missing keys.
  - Handle optional fields safely.
- Ensure that the JSON template in the Zendesk Trigger action matches the expected structure.

---

### 4. Authentication or Authorization Issues

**Symptoms:**
- Lambda returns 4xx or 5xx responses.
- Logs mention permission denied or missing credentials.

**Likely causes:**
- The Lambda execution role is missing required permissions.
- If calling Zendesk APIs from Lambda, the authentication token or secret is not configured correctly.

**Resolution:**
- Verify the Lambda execution role permissions.
- If using AWS Secrets Manager, confirm that:
  - The secret exists.
  - The Lambda role has permission to retrieve it.
- Confirm Zendesk API tokens or credentials are valid and correctly configured.

---

## Editing Considerations for Troubleshooting Guides

When editing troubleshooting content, I focus on:

- Using **clear, neutral language** to describe symptoms and causes.
- Structuring content into:
  - Symptoms  
  - Likely causes  
  - Resolutions  
- Avoiding blame or emotionally loaded wording.
- Ensuring that steps are **actionable** and **unambiguous**.

