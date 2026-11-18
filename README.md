# dmswift MCP server: Instagram for AI agents (Facebook Pages coming soon)

[dmswift](https://dmswift.com) is a hosted (remote) [Model Context Protocol](https://modelcontextprotocol.io) server that lets an AI agent run a business's Instagram professional account through Meta's official API: publish and schedule posts, reels, stories and carousels, run comment-to-DM campaigns, answer comments and direct messages, and read insights.

It works with Claude, Claude Code, ChatGPT, Cursor, Gemini, Grok, Perplexity, VS Code, n8n and any other MCP client that can reach a remote server.

```
https://app.dmswift.com/mcp
```

- **Transport:** Streamable HTTP (stateless, no SSE endpoint)
- **Auth:** OAuth 2.1 with dynamic client registration and Client ID Metadata Documents, or an API key as a bearer token
- **Tools:** 44, each with MCP annotations (`readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`)
- **Nothing to install or host.** There is no package to run and no Meta developer app to create. You sign in, connect your Instagram account, and paste the URL into your agent.

> **Platform status.** Instagram professional accounts (Business and Creator) are fully open. Facebook Pages are coming soon for new accounts, once Meta approves Page messaging for the app. dmswift does not support Facebook groups, and Meta's API does not cover personal Instagram accounts.

This repository holds the public documentation, client configuration examples and prompts for the server. The server itself is a hosted service; its source is not in this repository.

## Contents

- [What an agent can do](#what-an-agent-can-do)
- [Quick start](#quick-start)
- [Connect your client](#connect-your-client)
- [Tools](#tools)
- [Prompts and resources](#prompts-and-resources)
- [Safety model](#safety-model)
- [Meta's rules the server enforces](#metas-rules-the-server-enforces)
- [Example prompts](#example-prompts)
- [Pricing](#pricing)
- [FAQ](#faq)
- [Links](#links)

## What an agent can do

| Area | What the agent does |
|---|---|
| Publishing | Draft, schedule and publish feed photos, carousels (2 to 20), reels and stories. Draw text-on-background images without an image generator. |
| Comment to DM | Create a campaign: someone comments a keyword on a post or reel, gets a public reply and one private message with your link. Also triggers on DMs, story replies and mentions. |
| Comments | List comments, reply publicly, hide spam (reversible), delete when asked. |
| Inbox | Read conversations and reply inside Meta's 24-hour window, never while a person on your team took the conversation over. |
| Insights | Reach, views, followers and engagement per account and per post, campaign results, best times to post, and the questions nobody answered yet. |
| Contacts and knowledge | Search and tag the people who engaged, and read or add facts about the business so replies do not guess. |

## Quick start

1. Create an account at [dmswift.com](https://dmswift.com). The 7-day trial needs no card.
2. Connect your Instagram professional account with Instagram's own login. See [how to connect Instagram](https://dmswift.com/guides/connect-instagram).
3. Add `https://app.dmswift.com/mcp` to your AI client (next section) and sign in when it asks.
4. On the consent screen choose which channels the agent sees, what it may do (Read only, Read + draft, or Full), and whether every publish waits for your approval.
5. Ask your agent: *"List my channels on dmswift and tell me what you are allowed to do today."*

## Connect your client

| Client | How |
|---|---|
| **Claude** (web, desktop, mobile) | Customize, Connectors, Add custom connector. Name it dmswift, paste the URL, Connect, then sign in. [Guide](https://dmswift.com/mcp/claude) |
| **Claude Code** | `claude mcp add --transport http --scope user dmswift https://app.dmswift.com/mcp`, then `/mcp` and Authenticate |
| **ChatGPT** | chatgpt.com/plugins, +, Add custom MCP server, the URL, authentication OAuth, then install it. In a chat type `@` and pick it. Which plans may run write actions over a custom MCP server is set by OpenAI: see [OpenAI's help page](https://help.openai.com/en/articles/12584461-developer-mode-and-mcp-apps-in-chatgpt-beta). [Guide](https://dmswift.com/mcp/chatgpt) |
| **Cursor** | `~/.cursor/mcp.json`, see [examples/cursor-mcp.json](examples/cursor-mcp.json). [Guide](https://dmswift.com/mcp/cursor) |
| **VS Code** (Copilot agent mode) | `.vscode/mcp.json`, see [examples/vscode-mcp.json](examples/vscode-mcp.json) |
| **Gemini** | gemini.google.com, Settings, Connected Apps, Custom apps, Add a custom app. [Guide](https://dmswift.com/mcp/gemini) |
| **Grok** | grok.com/connectors, New Connector, Custom. [Guide](https://dmswift.com/mcp/grok) |
| **Perplexity** | Plugins, Add custom, Add MCP connector, Remote. [Guide](https://dmswift.com/mcp/perplexity) |
| **n8n, Make, Zapier** | The MCP Client node, module or app with the URL and an API key as a Bearer token. [Guide](https://dmswift.com/mcp/n8n) |
| **Any other MCP client** | Remote server, Streamable HTTP, the URL above. OAuth if the client supports it, otherwise an API key. See [examples/generic-api-key.json](examples/generic-api-key.json) |

API keys are created in the dashboard on the AI agents page. A key is weaker than the account that made it: it holds only the scopes you grant, can be pinned to specific channels, has daily caps, and cannot reach billing, the team or key management.

## Tools

Only the tools the connection's scopes allow are registered. Read tools are free; each write counts as one agent action against the plan's monthly allowance.

### Account and channels

| Tool | What it does |
|---|---|
| `get_account_status` | Permissions, channels, approval mode, daily caps left, plan and monthly usage |
| `list_pages` | The channels this connection may act on. Call it first |
| `check_page_permissions` | Whether a channel can publish |

### Posts and media

| Tool | What it does |
|---|---|
| `list_posts`, `list_live_posts`, `list_scheduled_posts`, `get_post` | Posts in dmswift and live posts and reels straight from the platform |
| `create_post`, `update_post`, `duplicate_post`, `delete_post` | Drafts and scheduled posts, with media, alt text, a first comment and an attached campaign |
| `publish_post` | Put a post live. Supports `dry_run` |
| `upload_media`, `request_upload_link`, `get_uploaded_media` | Media by public URL, by a one-time upload page for the user's own photos, or small base64 files |
| `render_text_card`, `get_brand_kit`, `set_brand_kit` | The server draws text-on-background images (single or carousel) in the channel's brand look |

### Campaigns (comment to DM, DM keywords, story replies, mentions)

| Tool | What it does |
|---|---|
| `list_campaigns`, `get_campaign` | Campaigns with hit counts |
| `create_campaign`, `update_campaign`, `delete_campaign`, `set_campaign_active` | Keyword rules with a public reply and a private message, per-person limits, caps, quiet hours and delays |
| `test_campaign_match` | Dry-run a sample comment against the campaigns before anything is live |

### Comments

| Tool | What it does |
|---|---|
| `list_comments` | Comments on live posts, newest first |
| `reply_to_comment`, `comment_on_post`, `like_comment` | Public replies (liking a comment is Facebook only; `comment_on_post` on Instagram is text only) |
| `hide_comment`, `delete_comment` | Moderation. Hiding is reversible and preferred |

### Inbox

| Tool | What it does |
|---|---|
| `list_conversations`, `get_conversation` | Conversations with `window_open` and `takeover_active` |
| `reply_to_conversation` | A private reply inside Meta's 24-hour window |

### Results

| Tool | What it does |
|---|---|
| `get_stats`, `get_activity_log`, `get_analytics` | Campaign totals, what the automation did and whether it worked, the funnel and best times to post |
| `get_unanswered_questions` | Comments that matched no campaign, ranked by how often they were asked |
| `get_insights`, `get_post_insights` | Meta's reach, views, followers and engagement |

### Contacts and knowledge base

| Tool | What it does |
|---|---|
| `list_contacts`, `tag_contact` | The people who engaged, searchable and taggable |
| `search_knowledge`, `add_knowledge` | What the business wrote about itself: prices, hours, policies |

The full reference, with scopes, arguments and error codes, is in the [API and MCP documentation](https://dmswift.com/docs).

## Prompts and resources

- Prompts: `launch_comment_campaign_for_latest_post`, `weekly_performance_report`
- Resources: `dmswift://docs/agent-guide` (how to use the server), `dmswift://pages` (the channels)

## Safety model

An agent that can publish needs limits that do not depend on the agent behaving well. These are enforced by the server:

- **Scopes.** Drafting (`posts:write`) and publishing (`posts:publish`) are separate. Read only, Read + draft and Full are one-click presets on the consent screen.
- **Approval mode.** Tick "Ask me before anything is published" and every publish waits for a person in the dashboard. The tool returns `pending_approval`, which is a success, not an error to retry.
- **Daily caps per connection.** By default 15 publishes, 30 comment replies and 100 private messages a day. A spent cap returns `daily_cap` with `resets_at`.
- **Channel pinning.** A connection can be limited to one account.
- **Idempotency.** `create_post`, `reply_to_comment` and `reply_to_conversation` accept an `idempotency_key`, so a retried call never double-posts.
- **Errors that say what to do.** A failed call carries `next_step`, one sentence the model can act on.
- **Audit and disconnect.** Everything an agent did is in the activity log, and Disconnect on the AI agents page ends a connection at once.

More on [security at dmswift](https://dmswift.com/security).

## Meta's rules the server enforces

- **One private reply per comment**, within 7 days of the comment ([Private Replies](https://developers.facebook.com/docs/instagram-platform/private-replies/)).
- **The 24-hour window.** After that, more messages only if the person answers, within 24 hours of their answer ([Instagram messaging](https://developers.facebook.com/docs/instagram-platform/instagram-api-with-instagram-login/messaging-api/)).
- **Nobody is messaged who did not write first.** There are no cold DMs and no bulk messages to followers.
- **Publishing cap.** Meta's [Content publishing](https://developers.facebook.com/docs/instagram-platform/content-publishing/) page states 100 API-published posts per Instagram account in a moving 24 hours (another section of the same page says 50). The server reads the account's live quota before each publish.
- **Instagram media cannot be deleted through the API.** `delete_post` removes it from dmswift only.

## Example prompts

More in [examples/prompts.md](examples/prompts.md).

```text
Make a comment campaign on my newest reel: whoever comments GUIDE gets a short public reply
and a DM with https://example.com/guide, once per person. Test it before you tell me it is live.
```

```text
Look at my last 10 Instagram posts and summarize the style. Then draft a carousel about
[topic] in the same voice, show it to me, and schedule it for tomorrow at 12:30 if I approve.
```

```text
Show me the questions in my comments that nobody answered this week, ranked by how often
they were asked, and suggest a campaign for the top three.
```

```text
Go over the open conversations from the last 24 hours. Who asked about price or availability
and is still waiting? Suggest a reply for each and send nothing until I say so.
```

## Pricing

MCP and API access are included in every plan, from Starter at $19 a month. The 7-day free trial includes Pro features with trial limits, and needs no card. Current plans and limits: [dmswift.com/pricing](https://dmswift.com/pricing).

## FAQ

**Do I need a Meta developer account or my own Meta app?**
No. dmswift's own Meta app makes the calls. You sign in with Instagram and grant access in Instagram's own dialog.

**Does it work with a personal Instagram account?**
No. Meta's API only covers professional accounts (Business or Creator). Switching is free and takes a minute in the Instagram app.

**Is this scraping or browser automation?**
No. Every call goes through Meta's official Graph API with the permissions you granted. The agent never sees your Instagram password.

**Can the agent DM anyone it wants?**
No. It can message only people who commented, replied to a story, mentioned you or wrote to you, and only inside the windows Meta allows.

**Can I self-host it?**
No. It is a hosted service. That is what removes the Meta app review, token handling and webhook hosting from your side.

**How is it different from ManyChat?**
ManyChat is built around a visual flow builder that a person operates. dmswift has a dashboard too, but it is built so that an AI agent can do the same work through MCP, with scopes, approval mode and daily caps. A side by side comparison: [dmswift vs ManyChat](https://dmswift.com/compare/manychat).

## Links

- Website: [dmswift.com](https://dmswift.com)
- MCP overview: [dmswift.com/mcp](https://dmswift.com/mcp)
- Instagram MCP server: [dmswift.com/mcp/instagram](https://dmswift.com/mcp/instagram)
- API and MCP reference: [dmswift.com/docs](https://dmswift.com/docs)
- Guide, auto DM people who comment: [dmswift.com/guides/auto-dm-instagram-comments](https://dmswift.com/guides/auto-dm-instagram-comments)
- Machine-readable summary: [dmswift.com/llms.txt](https://dmswift.com/llms.txt)
- Support: support@dmswift.com

## License

The documentation and examples in this repository are released under the [MIT License](LICENSE). dmswift, the hosted service, is a commercial product.
