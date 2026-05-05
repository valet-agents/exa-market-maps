This folder contains the source for a Skilled Agent originally built for the Valet runtime. Changes should follow the Skilled Agent open standard.

## Setup

### Connectors

- **exa-websets-mcp**: The Exa Websets MCP server. The agent uses it to create named websets per thesis, define entity enrichments (founder, funding stage, headcount, geo, recent news), run searches, and refresh existing maps. Add it from the catalog at the org level so other Exa-powered agents can share the same connector and key.

### Channels

- **slack** (slack): The agent's per-agent Slack bot. Listens for @mentions and replies in-thread. Slack writes use the bidirectional Slack catalog channel — there is no separate `slack-mcp` connector.

### Secrets

- **EXA_API_KEY**: Your Exa API key. Create one at [dashboard.exa.ai](https://dashboard.exa.ai) under **API Keys**. The key needs Websets access (the standard Exa key includes it). Stored as a secret and only injected into the MCP server URL — it is never echoed in agent replies.

### External Setup

1. After deploy, invite the agent's Slack bot to whichever channel(s) you want to be able to use it from — typically a deal-flow, research, or investing channel. The agent posts only in the threads where it's @mentioned.
2. To smoke-test, @mention the bot with a short thesis like *"map the vertical AI agents space"*. The agent should reply in-thread with a webset URL and a 10-result preview within a minute. Click the URL to see the full, live-updating map in Exa.
3. To refresh an existing map, @mention the bot in the same thread (or any thread) with *"refresh that map"* or *"add headcount to the AI infra map"*. The agent looks up which webset you mean from its memory of your recent maps.

## Customizing

- **Slack-only by design**: this template ships with one channel — Slack — to keep the v1 use case focused (interactive, thesis-driven map building). If you want a weekly auto-refresh of one or more saved websets, add a `cron` channel to `valet.yaml` and a matching `channels/cron.md` describing which websets to refresh and where to post the diff.
- **Default enrichments**: the SOUL workflow defines `founder`, `funding_stage`, `headcount`, `geo`, and `recent_news` as the default enrichments for every new webset. Edit the **Phase 2: Build** section in `SOUL.md` to add or drop enrichments globally.
- **Result cap**: the agent caps the in-thread preview at 10 results. Change the cap in the SOUL `Phase 3: Post` and `Always` guardrails.
