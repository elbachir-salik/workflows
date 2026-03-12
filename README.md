## n8n Local Setup & Workflows

This repo contains a simple `docker-compose` setup to run n8n locally and two example workflows:

- **AI Meeting → Notes → Tasks Agent** (`meeting-notes/ai-meeting-notes-agent.json`)
- **AI Lead Qualification Workflow** (`lead-qualification/ai-lead-qualification.json`)

Both are standard n8n workflow export JSON files that you can import into your own n8n instance.

---

## 1. Prerequisites

- **Docker** and **Docker Compose** installed
- An `.env` or secrets manager for any API keys you need (Zoom, Anthropic, OpenAI, Slack, Notion, Jira, SMTP, etc.)

> Note: The `n8n_data` folder is used to persist n8n data locally and is ignored by git.

---

## 2. Run n8n locally with Docker

From the root of this repo:

```bash
docker compose up -d
```

This uses `docker-compose.yml` to start an `n8nio/n8n` container with:

- **Port**: `5678` (UI at `http://localhost:5678`)
- **Basic auth**: `admin` / `password` (change these in `docker-compose.yml` before exposing to the internet)
- **Persistent data**: `./n8n_data` mounted into the container

To stop n8n:

```bash
docker compose down
```

---

## 3. Import the workflows into n8n

1. Open n8n in your browser: `http://localhost:5678`
2. Log in with the basic auth credentials configured in `docker-compose.yml`.
3. In the n8n UI, click **Workflows → Import from File**.
4. Import each JSON file:
   - `meeting-notes/ai-meeting-notes-agent.json`
   - `lead-qualification/ai-lead-qualification.json`
5. Save each imported workflow.

---

## 4. Configure credentials & environment

Before you run the workflows, set up the required credentials in n8n (top-right **Credentials** menu):

- **Zoom OAuth2**: used by the meeting-notes workflow to fetch meeting recordings/transcripts.
- **Anthropic API**: used by the meeting-notes workflow (`Claude AI - Summarize & Extract Tasks` node).
- **Notion API**: used to create tasks in a Notion database (update `YOUR_NOTION_DATABASE_ID` in the workflow or via expressions).
- **Jira API**: used to create Jira issues (update `YOUR_JIRA_PROJECT_KEY` in the workflow).
- **Slack API**: used to send meeting summaries to a Slack channel (update `#YOUR_SLACK_CHANNEL`).
- **OpenAI API**: used by the lead-qualification workflow for AI scoring.
- **Email / SMTP**: used by the lead-qualification workflow to send follow-up emails.

You will need to:

- Create the respective credentials in n8n with your keys/tokens.
- Optionally, replace placeholder IDs/keys (e.g. database IDs, project keys, email addresses, Slack channel) directly in the workflow nodes after import.

---

## 5. Using the Meeting Notes workflow

Workflow file: `meeting-notes/ai-meeting-notes-agent.json`

- **Trigger**: `Zoom Webhook` (`/zoom-meeting-ended` path)
- **Flow**:
  - Receives Zoom webhook when a meeting ends.
  - Fetches the meeting recordings, finds the transcript file, and downloads the transcript text.
  - Sends transcript to Anthropic (`Claude AI`) to:
    - Generate a concise meeting summary.
    - Extract structured action items (title, assignee, due date, priority).
  - Parses the AI response into `summary` + `tasks` JSON.
  - For each task:
    - Creates a task in **Notion**.
    - Creates a **Jira** issue.
  - Posts a summary + stats to **Slack**.

### Setup steps

1. In Zoom, configure a webhook to point to:
   - `http://<your-public-url>/webhook/zoom-meeting-ended`
   - In local development, use a tunnel (e.g. `ngrok`, `cloudflared`) to expose `http://localhost:5678`.
2. In n8n, open the imported workflow and:
   - Ensure the `Zoom Webhook` node path matches your Zoom webhook config.
   - Update any placeholder IDs/keys: Notion database ID, Jira project key, Slack channel.
3. **Activate** the workflow in n8n so it will run on incoming webhooks.

---

## 6. Using the Lead Qualification workflow

Workflow file: `lead-qualification/ai-lead-qualification.json`

- **Trigger**: `Lead Webhook` (`/lead-capture` path)
- **Flow**:
  - Receives a POST with lead info (`name`, `email`, `message`).
  - Sends the information to OpenAI to generate a short qualification.
  - Normalizes the data (`Format Lead` node).
  - Sends a follow‑up email to the lead.

### Setup steps

1. In n8n, open the imported `AI Lead Qualification Workflow`.
2. Confirm the webhook path (default: `/lead-capture`).
3. Configure the **OpenAI** credential and the **Email/SMTP** credential.
4. Update sender address, subject, and email body if needed.
5. **Activate** the workflow.

### Testing the webhook

With n8n running and the workflow active, send a POST request to:

```bash
curl -X POST "http://localhost:5678/webhook/lead-capture" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Jane Doe",
    "email": "jane@example.com",
    "message": "We are interested in your product for our 50-person team."
  }'
```

You should see the workflow execute in n8n and a follow‑up email sent.

---

## 7. Development tips

- **Change auth**: Before using this in anything but local dev, update the `N8N_BASIC_AUTH_USER` and `N8N_BASIC_AUTH_PASSWORD` in `docker-compose.yml`.
- **Persist data**: `n8n_data` holds your workflows, credentials metadata, etc. Do not commit this; it is already in `.gitignore`.
- **Export/import**: If you modify workflows in the UI and want to update the repo, export them from n8n as JSON and replace the existing files.

