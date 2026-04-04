# Smart Email Triage AI System

A production-grade, AI-powered email triage automation built with n8n, OpenAI, Gmail API, and Slack. This system continuously monitors an inbox, passes unread emails to an LLM for classification and summary extraction, intelligently routes them to appropriate workflows via an architectural Switch node, and pushes notifications for high-priority items.

## 🚀 Tech Stack
- **Workflow Engine:** [n8n](https://n8n.io/)
- **Large Language Model:** OpenAI / Anthropic API (via `@n8n/n8n-nodes-langchain.openAi`)
- **Integrations:** Gmail API (OAuth2), Slack (Bot Token)
- **Deployment Strategy:** AWS EC2 (t3.micro) or Google Cloud Compute Engine (e2-micro) Free Tier with Docker Compose and Caddy (Auto-HTTPS).

## 🧠 System Architecture
1. **Trigger:** Polls Gmail every 1 minute for unread messages.
2. **AI Processing:** Injects `Subject` and `Snippet` into a strict system prompt inside a LangChain OpenAI node to guarantee structured JSON output.
3. **Logic Routing:** A Switch Node evaluates the AI's `category` key and routes traffic to one of 4 independent branches:
   - Manager Priority (Requires Slack Alert)
   - Customer Support
   - Archive
   - Fallback (Manual AI Review for hallucinations)
4. **Action:** Gmail applies specific labels based on the route, and Slack messages a generated summary to a designated channel.

## 🛠️ How to Clone & Use This Yourself

### 1. Import Workflow to Your n8n Instance
1. Open your n8n dashboard.
2. Go to your Workflows list and click **Import from File**.
3. Select the `smart_email_triage.json` file from this repository.

### 2. Configure Credentials (Sanitized)
Because this repository sanitizes passwords and access tokens, you will need to re-authenticate the nodes to your own accounts:
1. **Gmail Trigger & Gmail Nodes:** Click on the nodes, select "Create New Credential", and set up Gmail OAuth2 using a Google Cloud Console App.
2. **AI Node:** Set up your OpenAI API token.
3. **Slack Node:** Go to `api.slack.com/apps`, create a bot, give it the `chat:write` scope, and paste the `xoxb-` Access Token into the credential popup.

## ☁️ Setting Up Cloud Hosting on AWS or GCP (Free Tier)

This repository includes a `docker-compose.yml` file to permanently host this on a virtual machine. 

Follow the guide in `AWS_DEPLOYMENT_GUIDE.md` for AWS EC2, or `GCP_DEPLOYMENT_GUIDE.md` for Google Cloud Compute Engine. Both guides provide step-by-step instructions on spinning up the server, configuring Swap Memory (crucial for 1GB RAM limits), and routing your custom domain with automatic HTTPS.
