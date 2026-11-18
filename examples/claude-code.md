# Claude Code

Add the server once for your user:

```bash
claude mcp add --transport http --scope user dmswift https://app.dmswift.com/mcp
```

Then run `/mcp` inside Claude Code, choose dmswift and Authenticate. Your browser opens the dmswift consent screen, where you choose the channels, what Claude Code may do and whether publishing waits for your approval.

With an API key instead of sign-in:

```bash
claude mcp add --transport http --scope user dmswift https://app.dmswift.com/mcp \
  --header "Authorization: Bearer fbcb_your_key_here"
```

Full guide: https://dmswift.com/mcp/claude
