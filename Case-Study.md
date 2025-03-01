**Dynatrace Alerting and Notification Integrations**

# 1. Sending Dynatrace Alerts to Slack

### Step 1: Create an Incoming Webhook in Slack

1. Go to Slack → Apps → Incoming Webhooks.
2. Click **Create New Webhook** and select the channel.
3. Copy the Webhook URL (e.g., `https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX`).

### Step 2: Create a Dynatrace Notification Integration

1. Go to **Dynatrace → Settings → Integration → Problem Notifications**.
2. Click **Add Notification → Custom Integration**.
3. Set the Webhook URL to your Slack webhook:
   ```
   https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX
   ```
4. Use this custom payload to format the Slack message:
   ```json
   {
     "text": ":warning: *Dynatrace Alert* :warning:",
     "attachments": [
       {
         "title": "{ProblemTitle}",
         "text": "{ProblemDetailsText}",
         "color": "#ff0000"
       }
     ]
   }
   ```
5. Click **Save and Test the Notification**.

✅ **Result:** Dynatrace alerts will now be sent to your Slack channel in real-time.

---

# 2. Integrating Dynatrace Alerts with PagerDuty

### Step 1: Create a PagerDuty Service

1. Go to **PagerDuty → Services → Create a New Service**.
2. Under **Integration Type**, select **Dynatrace**.
3. Copy the **Integration Key**.

### Step 2: Configure Dynatrace to Send Alerts to PagerDuty

1. In Dynatrace, go to **Settings → Integration → Problem Notifications**.
2. Click **Add Notification → PagerDuty**.
3. Enter the **Integration Key** from PagerDuty.
4. Click **Save and Send a Test Alert**.

✅ **Result:** Incidents will be automatically created in PagerDuty whenever an issue occurs.

---

# 3. Triggering AWS Lambda on Dynatrace Alerts (Auto-Remediation)

### Step 1: Create an AWS Lambda Function

1. Go to **AWS Lambda → Create Function → Author from Scratch**.
2. Set **Runtime** to **Python 3.9**.
3. Add the following Python script:
   ```python
   import json
   import boto3

   def lambda_handler(event, context):
       problem_details = json.loads(event['body'])
       instance_id = "i-1234567890abcdef"

       if "CPU usage high" in problem_details["ProblemTitle"]:
           ec2 = boto3.client('ec2')
           ec2.reboot_instances(InstanceIds=[instance_id])
           return {
               'statusCode': 200,
               'body': json.dumps('EC2 instance restarted')
           }
   ```
4. Deploy the function and copy the **Lambda API Gateway URL**.

### Step 2: Set Up a Dynatrace Webhook for Lambda

1. In Dynatrace, go to **Settings → Integration → Problem Notifications**.
2. Click **Add Notification → Custom Integration**.
3. Set the Webhook URL to your **Lambda API Gateway URL**.
4. Use this custom payload:
   ```json
   {
     "ProblemTitle": "{ProblemTitle}",
     "ProblemDetails": "{ProblemDetailsText}",
     "State": "{State}"
   }
   ```
5. Click **Save and Test the Webhook**.

✅ **Result:** If an EC2 instance’s CPU usage is too high, Dynatrace will trigger Lambda, which will automatically restart the instance.

---

# 4. Dynatrace Webhook Integration with JIRA

### Step 1: Create an API Token in JIRA

1. Go to **JIRA → Account Settings → Security**.
2. Click **Create API Token → Give it a name (e.g., Dynatrace Integration)**.
3. Copy the generated token.

### Step 2: Configure a Webhook in Dynatrace

1. Go to **Dynatrace → Settings → Integration → Problem Notifications**.
2. Click **Add Notification → Custom Integration**.
3. Use the following Webhook URL (replace `<JIRA-DOMAIN>` with your JIRA instance):
   ```
   https://<JIRA-DOMAIN>.atlassian.net/rest/api/2/issue/
   ```
4. Select **POST** as the **HTTP Method**.

### Step 3: Define the Webhook Payload

Enter the following JSON:

```json
{
  "fields": {
    "project": { "key": "SRE" },
    "summary": "{ProblemTitle}",
    "description": "Dynatrace detected an issue:\n{ProblemDetailsText}",
    "issuetype": { "name": "Bug" }
  }
}
```

Modifications:

- Replace **"SRE"** with your **JIRA project key**.
- Change **"Bug"** to another issue type if needed (e.g., **"Incident"**).

### Step 4: Add Authentication

In **Custom Headers**, add:

```json
{
  "Authorization": "Basic <Base64-encoded JIRA email:API token>",
  "Content-Type": "application/json"
}
```

#### How to Generate the Base64 Authentication Header?

Run the following command:

```bash
echo -n "your-email@example.com:JIRA-API-TOKEN" | base64
```

Copy the output and replace `<Base64-encoded JIRA email:API token>` in the header.

### Step 5: Test the Integration

1. Click **Save** in Dynatrace.
2. Click **Send Test Notification** to verify the setup.
3. A new JIRA issue should appear under your selected project (**SRE**).

✅ **Result:** Whenever Dynatrace detects a problem, it will automatically create a JIRA issue in real time.

---

These integrations help automate incident response and ensure that alerts are routed efficiently to the correct teams and systems for resolution.

