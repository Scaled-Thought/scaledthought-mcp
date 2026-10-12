# ScaledThought MCP server

[ScaledThought](https://scaledthought.com/go/org-ghrepo) is an OpenAI-compatible API for open-weight models, set up
by your coding agent. This repository holds the public metadata for its remote MCP server: this README, the
[MCP Registry](https://registry.modelcontextprotocol.io) entry (`server.json`, name `com.scaledthought/mcp`) and a
security policy. It contains no application source code.

- **OpenAI-compatible API for open-weight models.** Any OpenAI SDK works with the base URL
  `https://api.us.scaledthought.com/v1`.
- **Your agent signs up from one pasted prompt.** It creates the account and the API key itself.
- **Prepaid credits, no subscription.** The agent asks you to approve a top-up through an approval link; you pay only
  for what you use.
- **Budgets per key.**
- **Prompts and completions are not stored or logged.** Every response carries `x-st-data-retention: none`.
- **US region live; Canada coming soon.**

The models live in the US right now and their prices: <https://scaledthought.com/pricing>, or
`npx -y scaledthought@latest models --live`.

## 1. Get an API key

Paste this into your coding agent (Claude Code, Codex, Cursor, Cline, OpenCode and others):

```text
Set me up on ScaledThought: scaledthought.com/go/org-ghrepo
```

Or run the CLI yourself:

```bash
npx -y scaledthought@latest init --region us --source org-ghrepo
```

`init` writes `ST_API_KEY` and `ST_BASE_URL` to `./.env`. Usage is prepaid: before the first call, the agent prints an
approval link for you to add credits by card.

## 2. Connect the MCP server

Remote server, streamable HTTP, authenticated with your API key:

- URL: `https://api.us.scaledthought.com/mcp?source=org-ghrepo`
- Header: `Authorization: Bearer <your ScaledThought API key>`

Claude Code (run it in the directory where `init` wrote `./.env`):

```bash
set -a; . ./.env; set +a
claude mcp add --transport http scaledthought 'https://api.us.scaledthought.com/mcp?source=org-ghrepo' \
  --header "Authorization: Bearer $ST_API_KEY"
```

A project `.mcp.json` (Claude Code fills in `${ST_API_KEY}` from the environment: export it from `./.env` before
starting Claude Code):

```json
{
  "mcpServers": {
    "scaledthought": {
      "type": "http",
      "url": "https://api.us.scaledthought.com/mcp?source=org-ghrepo",
      "headers": { "Authorization": "Bearer ${ST_API_KEY}" }
    }
  }
}
```

Cline (`cline_mcp_settings.json`; paste the `ST_API_KEY` value from `./.env` in place of `<your key>`):

```json
{
  "mcpServers": {
    "scaledthought": {
      "type": "streamableHttp",
      "url": "https://api.us.scaledthought.com/mcp?source=org-ghrepo",
      "headers": { "Authorization": "Bearer <your key>" }
    }
  }
}
```

## What the server does

It manages your ScaledThought account from the agent: account status and settings, API keys, budgets, usage,
prepaid top-ups (approval links), webhooks, model search and docs search. Inference itself goes to the
OpenAI-compatible API at `https://api.us.scaledthought.com/v1`.

## Links

- Docs for agents: <https://scaledthought.com/llms.txt>
- CLI on npm: [`scaledthought`](https://www.npmjs.com/package/scaledthought)
- Security and vulnerability disclosure: [SECURITY.md](SECURITY.md)

## License

The text and metadata in this repository are MIT licensed ([LICENSE](LICENSE)). The ScaledThought name and logo are
trademarks of ScaledThought Inc. and are not licensed.
