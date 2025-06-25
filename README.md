# FINANCIAL-MANGER-AND-FRAUD-DETECTOR
FINANCIAL MANGER AND FRAUD  DETECTOR
# Automated Financial Watchdog 🛡️

![n8n](https://img.shields.io/badge/n8n-Workflow_Automation-7722FF?style=for-the-badge)
![AI](https://img.shields.io/badge/AI_Powered-Gemini_/_Mistral-4285F4?style=for-the-badge)
![Google Sheets](https://img.shields.io/badge/Google_Sheets-Logging-34A853?style=for-the-badge)


An intelligent, end-to-end n8n workflow that automatically tracks your bank transactions, analyzes them for fraud using AI, logs them for a complete financial overview, and sends you real-time alerts for high-risk activity.

This project turns your transaction alerts into actionable intelligence, providing peace of mind with a system you control completely.

---

## 🌟 Key Features

-   **Real-time & Automated**: Triggers instantly the moment a new transaction email arrives. No manual intervention needed.
-   **AI-Powered Analysis**: Uses a powerful Large Language Model (like Google's Gemini or Mistral AI) to not only extract data but also to understand the context and assign a fraud score.
-   **Centralized Logging**: Automatically populates a Google Sheet with every transaction, creating a clean, structured dataset perfect for building personal finance dashboards.
-   **Conditional Smart Alerts**: You only get notified when it matters. The workflow uses logic to send a detailed alert to your primary email only if a transaction's fraud score exceeds a customizable threshold.
-   **Seamless Mobile Integration**: Starts right from your phone! Uses an SMS Forwarder app to bridge the gap between your bank's SMS alerts and the cloud-based workflow.

---

## ⚙️ How It Works: The Workflow Pipeline

This diagram shows the journey of your data from a simple bank alert to an intelligent insight.
[📱 Phone: Bank SMS Received] -> [SMS Forwarder App] -> [📧 Dedicated Gmail Inbox]
|
V
[🤖 n8n Workflow: Gmail Trigger] -> [🧠 AI Node (Gemini/Mistral)] -> [📊 Google Sheets Node] -> [❓ IF Node (Check Score)] -> [🚨 Email Alert Node (If High-Risk)]


---

## ✅ Prerequisites

Before you begin, you will need free accounts for the following services:

1.  **n8n Instance**: A running instance of n8n (either on [n8n Cloud](https://n8n.io/) or self-hosted).
2.  **Dedicated Gmail Account**: A **new, separate** Gmail account to receive the forwarded transaction messages (e.g., `my.transaction.tracker@gmail.com`).
3.  **Primary Email Account**: Your main email where you want to receive high-risk alerts.
4.  **Google Cloud Project**: To get API credentials for the Gmail and Google Sheets nodes. [Go here](https://console.cloud.google.com/).
5.  **AI Provider API Key**:
    -   **For Google Gemini**: An API key from [Google AI Studio](https://aistudio.google.com/app/apikey).
    -   **For Mistral/Other models**: An API key from [Hugging Face](https://huggingface.co/settings/tokens).
6.  **SMS Forwarder App**: An app on your Android phone to forward SMS to email (e.g., "SMS Forwarder" by Yasmani).

---

## 🚀 Setup Guide

Follow these steps carefully to get your Financial Watchdog up and running.

### Step 1: Import the n8n Workflow

1.  Download the `workflow.json` file from this repository.
2.  Open your n8n canvas.
3.  Click **"Import from File"** and select the `workflow.json` file.
4.  Alternatively, copy the raw JSON content and paste it directly onto the blank n8n canvas. The full workflow should appear.

### Step 2: Configure External Services

1.  **Google Cloud Console**:
    -   Create a new project.
    -   Enable the **Gmail API** and **Google Sheets API**.
    -   Go to "Credentials" -> "Create Credentials" -> "OAuth client ID".
    -   Select "Web application".
    -   Under "Authorized redirect URIs", add the OAuth Redirect URL provided by n8n in the credential setup window.
    -   Save the **Client ID** and **Client Secret**.

2.  **Google Sheet**:
    -   Create a new Google Sheet.
    -   In the first row, create these exact headers: `Timestamp`, `Amount`, `Merchant`, `TransactionID`, `FraudScore`, `Reason`.

3.  **SMS Forwarder App**:
    -   Install the app on your phone.
    -   Set up a rule to forward any SMS from your bank's sender ID to your dedicated Gmail account.

### Step 3: Configure the n8n Nodes

Now, go through the workflow in n8n and connect your accounts.

1.  **Trigger: New Transaction Email (Gmail Node)**
    -   **Credentials**: Click "Create New", and enter the Client ID and Secret you got from the Google Cloud Console. Sign in with your **dedicated** transaction Gmail account.
    -   **Filters**: In the node options, filter for emails from your bank's sender to prevent accidental triggers.

2.  **AI Brain (Gemini/HTTP Request Node)**
    -   **Credentials**: Create a new credential and paste your API key from Google AI Studio or Hugging Face.
    -   **Prompt/Body**: Ensure the prompt is correctly configured to read the email body from the previous step. The variable should be `{{ $json.body }}` or similar.

3.  **Log to Google Sheets (Google Sheets Node)**
    -   **Credentials**: Use the same Google credentials you created for the trigger node.
    -   **Sheet ID**: Paste the ID of your Google Sheet. (You can find this in the sheet's URL: `.../spreadsheets/d/`**`THIS_IS_THE_ID`**`/edit`).
    -   **Mapping**: Ensure the columns in the node correctly map to the JSON output from the AI node (e.g., `amount` maps to `{{ $json.amount }}`).

4.  **IF Node: The Gatekeeper**
    -   This node is pre-configured to check if the `fraud_score` is greater than 80. You can change this threshold to whatever you like.

5.  **The Alert (Final Gmail Node)**
    -   **Credentials**: Use the same Google credentials again.
    -   **To Address**: Enter your **primary** email address.
    -   **Subject & Body**: Customize the alert message. Use variables from the AI and Sheets nodes to make it informative!

### Step 4: Activate!

-   Test each step of the workflow to ensure it works.
-   Once you're satisfied, click the **"Active" toggle** at the top right of the canvas.
-   **Save** your workflow.

---

## 💡 Customization

This workflow is a powerful template. Here are ways you can customize it:

-   **Change the AI Model**: Swap out the Gemini/Mistral node for an OpenAI node.
-   **Refine the AI Prompt**: Edit the prompt in the AI node to change its behavior, ask for more details, or adjust its risk sensitivity.
-   **Add More Alerts**: Add nodes to send alerts via Slack, Telegram, or WhatsApp (e.g., using CallMeBot) in parallel with the email alert.
-   **Enhance the Dashboard**: Use the data in your Google Sheet to build a rich financial dashboard in Google Looker Studio or another BI tool.

---

## troubleshooting

-   **"localhost" Error during OAuth**: If you are self-hosting, ensure you have set the `WEBHOOK_URL` environment variable in your n8n configuration to your public-facing n8n URL.
-   **Data Not Extracted Correctly**: The AI's output can sometimes vary. Check the output of the AI node. You may need to refine your prompt to make it more specific and force a consistent JSON output.
-   **Permissions Errors**: Ensure you have enabled both the Gmail API and Google Sheets API in your Google Cloud Project.

---

