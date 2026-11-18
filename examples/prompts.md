# Prompts to try with the dmswift MCP server

Copy any of these into Claude, ChatGPT, Cursor, Gemini, Grok or another connected agent. Each one names the tools the agent is expected to call, so you can check its work.

## First run

```text
Which channels do you see on dmswift? For each one tell me the platform, and check what you
are allowed to do today: approval mode and what is left of the daily caps.
```

Tools: `list_pages`, `get_account_status`

## Comment to DM

```text
Make a comment campaign on my newest reel: whoever comments GUIDE gets a short public reply
and a DM with https://example.com/guide, once per person. Test it with the comments
"guide please" and "where is the guide?" before you tell me it is live.
```

Tools: `list_pages`, `list_live_posts`, `create_campaign`, `test_campaign_match`

```text
How many people triggered the GUIDE campaign this week, how many got the message, and how
many tapped the link?
```

Tools: `list_campaigns`, `get_stats`, `get_activity_log`

## Publishing

```text
Look at my last 10 Instagram posts and summarize the style: length, tone, emoji, call to
action. Then draft a post about [topic] in the same style and show it to me before you
schedule anything. If I approve, schedule it for tomorrow at 12:30.
```

Tools: `list_live_posts`, `create_post`, `publish_post` (with `dry_run` first)

```text
Turn these five tips into an Instagram carousel with text on a clean background, in my
brand colors. Show me the draft, do not publish.
```

Tools: `get_brand_kit`, `render_text_card`, `create_post`

## Comments

```text
Bring me every comment from the last week that has no reply yet. Sort them into questions,
compliments and spam. Suggest replies for the questions. Hide the spam, do not delete it.
```

Tools: `list_comments`, `search_knowledge`, `reply_to_comment`, `hide_comment`

## Inbox

```text
Go over the open conversations from the last 24 hours. Who asked about price or availability
and is still waiting? Suggest a reply for each one and send nothing until I say so.
```

Tools: `list_conversations`, `get_conversation`, `reply_to_conversation`

## Reports

```text
Use the weekly_performance_report prompt for my Instagram account.
```

```text
Compare my last 10 posts by reach and engagement. What do the top three have in common, and
what should I have done differently on the bottom three?
```

Tools: `get_insights`, `get_post_insights`, `get_analytics`

```text
Show me the questions in my comments that nobody answered, ranked by how often they were
asked, and suggest a campaign for the top three.
```

Tools: `get_unanswered_questions`, `search_knowledge`, `create_campaign`

## Knowledge base

```text
Add to the knowledge base: we are open Sunday to Thursday 9:00 to 18:00, shipping is free
over $50, and we have no physical store.
```

Tools: `add_knowledge`

## A good habit

End publishing prompts with "show me before you publish". For a stricter guarantee, tick
"Ask me before anything is published" when you connect: every publish then waits for your
approval in the dashboard, whatever the prompt says.
