# Deal Flow HITL Agent

Inbound from Typeform, Gmail, or LinkedIn → Claude qualifies + researches → you approve in Telegram → saved to Notion CRM.

## What it does

1. Watches 3 sources simultaneously for inbound B2B opportunities
2. Claude scores each one 1–10 and filters out low-quality leads automatically
3. For qualified leads (score ≥ 5), Claude writes a research brief
4. You get a Telegram message with the full brief + 3 buttons: **Add to CRM / Research more / Ignore**
5. Tap a button — the agent executes your decision instantly
6. "Research more" triggers a deeper Claude analysis and loops back for a second approval

## What you need

| Credential | Where to get it | Cost |
|-----------|----------------|------|
| Telegram Bot token | [@BotFather](https://t.me/botfather) | Free |
| Anthropic API key | [console.anthropic.com](https://console.anthropic.com) | ~$0.02–0.05 per lead |
| Notion API key | [notion.so/my-integrations](https://www.notion.so/my-integrations) | Free |
| Gmail OAuth2 | Google Cloud Console | Free |

## Notion database setup

Create a database with these exact properties:

| Property | Type |
|---------|------|
| Title (auto) | Title |
| Company | Text |
| Email | Email |
| Source | Select (Typeform, Gmail, LinkedIn) |
| Score | Number |
| Intent | Select (partnership, integration, sponsorship, collaboration, unclear) |
| Status | Select (New, In Progress, Closed Won, Closed Lost) |
| Fit Reason | Text |
| Research Brief | Text |
| Received At | Date |

## The Telegram approval message

Every qualified lead looks like this in your Telegram:

```
🎯 New B2B Opportunity

👤 Sarah Chen — Acme Automations
📥 Source: Gmail
⭐ Score: 7/10
🎯 Intent: integration
⚡ Urgency: medium

💬 Their message:
"Hi, we build no-code tools for SMBs and we'd love to 
integrate your workflows into our marketplace..."

🔍 Research brief:
Acme Automations is a 3-year-old SaaS with ~2,000 customers
in the SMB space. They have a marketplace of 50+ integrations
and ~15k followers across LinkedIn/Twitter...

─────────────────
What do you want to do?

[✅ Add to CRM] [🔬 Research more] [❌ Ignore]
```

## How to import

1. Download `deal-flow-hitl-agent.json`
2. In n8n: **Workflows → Import from file**
3. Replace all `YOUR_*` placeholders
4. Set up Notion database (schema above)
5. Activate — the 3 triggers start watching immediately

## LinkedIn workaround

LinkedIn has no native webhook. Use one of these:
- **Phantombuster** (free tier) — "LinkedIn Message Sender" phantom can forward messages
- **Make.com** — has a native LinkedIn Messages trigger, webhook to n8n
- **Manual** — copy-paste messages into the Typeform as a fallback

## Cost per lead processed

- Score < 5 (filtered): ~$0.001 (just the qualifier call)
- Score ≥ 5, you ignore: ~$0.03 (qualifier + research brief)
- Score ≥ 5, you add to CRM: ~$0.03
- Score ≥ 5, research more: ~$0.08 (qualifier + brief + deep research)