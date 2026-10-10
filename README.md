# @three-ws/knock-mcp

<!-- three.ws:badges -->
[![GitHub stars](https://img.shields.io/github/stars/nirholas/knock-mcp?style=flat&logo=github)](https://github.com/nirholas/knock-mcp/stargazers) [![License](https://img.shields.io/github/license/nirholas/knock-mcp?style=flat)](https://github.com/nirholas/knock-mcp/blob/HEAD/LICENSE) [![Last commit](https://img.shields.io/github/last-commit/nirholas/knock-mcp?style=flat)](https://github.com/nirholas/knock-mcp/commits) [![PRs welcome](https://img.shields.io/badge/PRs-welcome-brightgreen?style=flat)](https://github.com/nirholas/knock-mcp/pulls) [![AI agent friendly](https://img.shields.io/badge/AI%20agents-AGENTS.md%20%2B%20llms.txt-6d5dfc?style=flat)](https://github.com/nirholas/knock-mcp/blob/HEAD/AGENTS.md)
<!-- /three.ws:badges -->


Let an agent reach a real person.

Knock is a priced door to a human. Every [three.ws](https://three.ws) account
can publish one at `three.ws/knock/<handle>`, and the owner sets what one
message from a stranger costs. Paying that price buys exactly one message
through: it lands in their inbox, and their 3D companion walks on screen
wherever they are on the site and delivers it out loud, naming the sender and
the amount. The USDC settles **directly to the recipient**.

This MCP server gives any agent client (Claude Code, Claude Desktop, Cursor,
Windsurf, or your own) the tools to find a reachable person, knock, and read
the answer.

## This server holds no wallet

On a **free** door, `knock_send` sends immediately, because nothing moves.

On a **priced** door, `knock_send` does not pay and cannot pay. It returns the
recipient, the amount, the token, the chain and the endpoint, and stops. The
human approves the spend and pays from the wallet they already control, with
[`@three-ws/knock`](https://www.npmjs.com/package/@three-ws/knock) or any other
x402 client.

That is deliberate. An MCP server with a key in an environment variable should
not be able to move a person's money on its own.

## Install

Claude Code:

```bash
claude mcp add knock -- npx -y @three-ws/knock-mcp
```

Any MCP client, by config:

```json
{
	"mcpServers": {
		"knock": {
			"command": "npx",
			"args": ["-y", "@three-ws/knock-mcp"]
		}
	}
}
```

Add `THREE_WS_API_KEY` only if you also want the two owner-side tools:

```json
{
	"mcpServers": {
		"knock": {
			"command": "npx",
			"args": ["-y", "@three-ws/knock-mcp"],
			"env": { "THREE_WS_API_KEY": "sk_live_…" }
		}
	}
}
```

Node 20+.

## Tools

| Tool | Credential | What it does |
| --- | --- | --- |
| `knock_quote` | none | What one person charges for a single message, which chains they take it on, the length limit, and the endpoint. |
| `knock_directory` | none | Everyone reachable right now, cheapest first. |
| `knock_send` | none | Get one message through. Free doors send; priced doors are quoted for a human to approve. |
| `knock_receipt` | none | What became of a knock you sent: pending, read, replied (with the reply), or dismissed. |
| `knock_inbox` | `THREE_WS_API_KEY` | The knocks **your** door has taken, each with what the sender paid, plus totals. |
| `knock_act` | `THREE_WS_API_KEY` | Reply to, mark read, dismiss, or block one of them. |

The owner tools are account-scoped server-side. They can never read or change
another account.

## What a session looks like

> **You:** Find someone on three.ws who takes questions about x402, and ask them
> how their Solana facilitator handles fee payers.

The agent calls `knock_directory`, picks a door, calls `knock_quote` to get the
live price, and comes back with:

```
nirholas (@nirholas) charges $0.05 USDC on solana.
Endpoint: https://three.ws/api/x402/knock?to=nirholas
The payment settles directly to the recipient.
Approve?
```

You approve and pay from your own wallet:

```bash
npx @three-ws/knock send nirholas \
  "How does your Solana facilitator handle the fee payer?" \
  --from "Ada (research agent)" --subject "x402 fee payer" --payer ./payer.mjs
```

Their companion walks on screen and says: *"Ada paid $0.05 to reach you: x402
fee payer."* When they answer, the agent reads it back with `knock_receipt` on
the receipt URL. No account on either side beyond the door owner's.

## Environment

| Variable | Required | Default | Purpose |
| --- | --- | --- | --- |
| `THREE_WS_API_KEY` | for `knock_inbox` / `knock_act` only | none | API key or OAuth token for the account whose door you manage. Aliases: `THREE_WS_TOKEN`, `THREE_WS_BEARER`. |
| `THREE_WS_BASE` | no | `https://three.ws` | Override only when self-hosting. |
| `THREE_WS_TIMEOUT_MS` | no | `20000` | Per-request timeout. |

## Run and inspect

```bash
node src/index.js                     # stdio
npm run inspect                       # MCP Inspector
npm test                              # the tool surface and the no-wallet rule
```

## Related

- **[@three-ws/knock](https://www.npmjs.com/package/@three-ws/knock)** is the SDK and CLI that actually pays.
- **[three.ws/knock](https://three.ws/knock)** to open your own door.
- **[Knock docs](https://three.ws/docs/knock)** for the protocol and the wire format.

## License

Apache-2.0

<!-- three.ws:growth -->
## Support the project

If knock-mcp saves you time, **[star it on GitHub](https://github.com/nirholas/knock-mcp)**. Stars are how other developers and AI agents find the repositories worth trusting, and they cost you one click.

Know someone who would use it? [Post on X](https://twitter.com/intent/tweet?text=knock-mcp%3A%20Let%20an%20agent%20reach%20a%20real%20person&url=https%3A%2F%2Fgithub.com%2Fnirholas%2Fknock-mcp) · [Share on Bluesky](https://bsky.app/intent/compose?text=knock-mcp%3A%20Let%20an%20agent%20reach%20a%20real%20person%20https%3A%2F%2Fgithub.com%2Fnirholas%2Fknock-mcp) · [Share on LinkedIn](https://www.linkedin.com/sharing/share-offsite/?url=https%3A%2F%2Fgithub.com%2Fnirholas%2Fknock-mcp) · [Submit to Hacker News](https://news.ycombinator.com/submitlink?u=https%3A%2F%2Fgithub.com%2Fnirholas%2Fknock-mcp&t=knock-mcp%3A%20Let%20an%20agent%20reach%20a%20real%20person) · [Share on Reddit](https://www.reddit.com/submit?url=https%3A%2F%2Fgithub.com%2Fnirholas%2Fknock-mcp&title=knock-mcp%3A%20Let%20an%20agent%20reach%20a%20real%20person)

## Built for AI agents too

Coding agents and LLM tooling can read this repo directly: [AGENTS.md](./AGENTS.md), [llms.txt](./llms.txt), [llms-full.txt](./llms-full.txt). Point an agent at `https://github.com/nirholas/knock-mcp` and it has the context it needs.

## More from the same author

- [All repositories by nirholas](https://github.com/nirholas/nirholas#readme): the full catalog, grouped by topic
- [three.ws](https://three.ws): the platform for 3D AI agents with Solana wallets, a skill marketplace and x402 payments
- Questions or ideas: [open an issue](https://github.com/nirholas/knock-mcp/issues) or [start a discussion](https://github.com/nirholas/knock-mcp/discussions)

## Contributors

[![Contributors](https://contrib.rocks/image?repo=nirholas/knock-mcp)](https://github.com/nirholas/knock-mcp/graphs/contributors)

## Star history

[![Star History Chart](https://api.star-history.com/svg?repos=nirholas/knock-mcp&type=Date)](https://www.star-history.com/#nirholas/knock-mcp&Date)

<!-- /three.ws:growth -->

<!-- three.ws:ecosystem -->
## Part of three.ws

[three.ws](https://three.ws) is a platform for 3D AI agents with Solana wallets: avatars, a skill marketplace, x402 payments and more than seventy MCP servers. `@three-ws/knock-mcp` is one package from it.

- Documentation site: https://nirholas.github.io/knock-mcp/
- npm: https://www.npmjs.com/package/@three-ws/knock-mcp
- Canonical source: https://github.com/nirholas/three.ws (this repository is a generated mirror of `packages/knock-mcp`)
- Agent-readable summary: [llms.txt](./llms.txt) and [AGENTS.md](./AGENTS.md)
- Issues and ideas: https://github.com/nirholas/knock-mcp/issues
<!-- /three.ws:ecosystem -->
