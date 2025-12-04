# Setup Instructions – Edited Version (AWS x Zendesk Integration)

This guide provides **step-by-step instructions** to configure a Zendesk → AWS → Zendesk integration using **Zendesk Webhooks, Amazon API Gateway, and AWS Lambda**.

It is written for a **technical administrator or engineer** who has basic familiarity with AWS and Zendesk.

---

## Prerequisites

Before you begin, ensure that:

- You have an **AWS account** with permissions to create:
  - API Gateway REST APIs
  - Lambda functions
  - IAM roles and policies
- You have a **Zendesk account** with permissions to:
  - Create or edit **Triggers** (or Automations)
  - Create **Webhooks**
- You have a basic **Lambda function** created (even a placeholder) that:
  - Uses a runtime such as **Node.js** or **Python**
  - Accepts events from API Gateway

---

## Step 1 – Configure the Lambda Function

1. In the **AWS Management Console**, open the **Lambda** service.
2. Create or select the Lambda function that will handle Zendesk events.
3. Confirm that:
   - The function has an **execution role** with permission to write logs to **CloudWatch Logs**.
   - (Optional) If the function will call the Zendesk API, attach the required permissions to access any supporting services (for example, AWS Secrets Manager for API tokens).

---

## Step 2 – Create an API Gateway Endpoint

1. Open **Amazon API Gateway** in the AWS Console.
2. Create a **new REST API** (or select an existing one for integrations).
3. Create a **resource** (for example, `/zendesk-webhook`).
4. Under this resource, create a **POST method**.
5. Integrate the POST method with your **Lambda function**:
   - Integration type: *Lambda Function*
   - Select the correct region and function name.
6. **Deploy** the API to a stage (for example, `prod`).
7. Copy the **Invoke URL** for the POST method.  
   You will use this URL in Zendesk.

---

## Step 3 – Configure the Zendesk Webhook

1. In Zendesk, go to **Admin Center → Apps and Integrations → Webhooks**.
2. Create a **new Webhook**:
   - **Name:** `AWS Lambda Webhook`
   - **Endpoint URL:** paste the **API Gateway Invoke URL** from Step 2.
   - **Request method:** `POST`
   - **Request format:** `JSON`
3. Set the **authentication** method if required (for example, a shared secret or custom header, depending on your design).

---

## Step 4 – Connect the Webhook to a Trigger

1. In Zendesk, go to **Admin Center → Objects and rules → Triggers**.
2. Create a new **Trigger** (for example, “Send ticket events to AWS Lambda”).
3. Define the **conditions** under which this Trigger fires (for example:  
   - Ticket is created  
   - or Ticket status changes to `Open`).
4. Under **Actions**, configure:
   - **Notify active webhook**
   - Select the webhook created in Step 3.
5. In the JSON body of the action, include the ticket data you want to send to AWS (for example: ticket ID, requester email, status).

---

## Step 5 – Test the Integration

1. In Zendesk, create a **test ticket** that satisfies the Trigger conditions.
2. Verify that:
   - The Trigger fires.
   - The webhook is called without error.
3. In AWS:
   - Open **CloudWatch Logs** for your Lambda function.
   - Confirm that the function received and logged the expected payload.
4. If the function calls back into Zendesk, confirm that the **ticket is updated** as expected.

---

## Global English and Clarity Considerations

While editing these instructions, I:

- Replaced informal phrasing with **precise, neutral language**.
- Added **explicit preconditions** and **step-by-step structure**.
- Standardized terminology (for example, always using “AWS Management Console” and “Zendesk Trigger”).
- Ensured sentences are short and direct to improve **translatability** and **global comprehension**.

