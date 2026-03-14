# Deep Research Agent

Type any topic into Telegram → get a full cited research report in Google Docs in ~90 seconds.

## What it produces

Every report has 5 sections:
- **Executive summary** — the key insight in 3-4 sentences
- **Key findings** — 5-7 bullet points with cited sources
- **Pros & cons** — balanced view of advantages and risks
- **Competitor & people landscape** — who the key players are
- **Action recommendations** — 3-5 concrete next steps

## What you need

| Credential | Where to get it | Cost |
|-----------|----------------|------|
| Telegram Bot token | [@BotFather](https://t.me/botfather) on Telegram | Free |
| Anthropic API key | [console.anthropic.com](https://console.anthropic.com) | ~$0.05 per report |
| SerpAPI key | [serpapi.com](https://serpapi.com) | 100 free/month |
| Google Docs OAuth2 | Google Cloud Console | Free |

## How to import

1. Download `research-agent.json`
2. In n8n: **Workflows → Import from file**
3. Add your 4 credentials (see table above)
4. Replace `YOUR_ANTHROPIC_API_KEY` and `YOUR_SERPAPI_KEY` in the HTTP nodes
5. Activate the workflow
6. Message your Telegram bot any research topic

## How it works

1. You send a topic to your Telegram bot
2. Claude breaks it into 5 targeted search queries
3. SerpAPI runs all 5 searches, collects top results
4. Claude reads all results and writes a structured report
5. Report is saved as a new Google Doc
6. Telegram sends you back the link

## Example

**Input (Telegram):** `AI agents in healthcare 2025`

**Output (Google Docs):** A ~1500 word report covering market overview, key players like Google DeepMind and Hippocratic AI, regulatory challenges, clinical use cases, and 5 action recommendations — all with source links.

## Estimated cost per report

- Claude API: ~$0.03–0.08 depending on report length
- SerpAPI: 5 credits (100 free/month = 20 free reports/month)
- Total: effectively free for personal use

## Screenshot

_Add screenshot here after first run_