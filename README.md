# Paywalls.ai

Monetize AI assistants, agents, and MCP tools without building billing. Change a URL, set a price, and get paid.

## Why Paywalls

- OpenAI‑compatible proxy: swap `baseURL` and key; keep your client code.
- Usage‑based pricing: per‑model token metering, per‑request minimums, and manual charges.
- Built‑in payments: Shared mode (hosted top‑ups) or Default mode (your Stripe/custom rails).
- Zero extra UI: if auth/top‑up is needed, we return a normal assistant message with the right link.
- Ledger & analytics: real‑time events, users, revenue/cost/profit.

## How it works

1. Your app calls an OpenAI‑style endpoint via Paywalls.
2. We identify the end user, check authorization and balance.
3. If action is required, we return an assistant message (render as‑is).
4. Otherwise we forward the request to the model, meter usage, and write charges to the ledger.

### Modes

- Shared mode: hosted top‑ups; users fund a shared wallet; your earnings accrue on usage. Fastest to launch.
- Default mode: bring your own Stripe or rails; users hold app‑scoped balances; you credit via deposits.

---

## Quickstart

```ts
import OpenAI from "openai";
const client = new OpenAI({
  apiKey: process.env.PAYWALLS_API_KEY, // keep server/edge only
  baseURL: "https://api.paywalls.ai/v1",
});
const res = await client.chat.completions.create({
  model: "openai/gpt-4o-mini",
  user: "user_123", // required for billing/authorization
  messages: [{ role: "user", content: "Hello!" }],
});
console.log(res.choices[0]?.message?.content);
```

cURL

```bash
curl https://api.paywalls.ai/v1/chat/completions \
  -H "Authorization: Bearer $PAYWALLS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model":"openai/gpt-4o-mini",
    "user":"user_123",
    "messages":[{"role":"user","content":"Hello!"}]
  }'
```

Notes

- Always include a stable, pseudonymous user id (body `user` recommended; header `X‑Paywall‑User` supported).
- Never expose your paywall key in browsers; call from a server or edge function.

---

## Common integrations

- SDKs: official OpenAI SDKs (Node, Python, .NET, Java) and community clients (Go, Ruby, Rust, PHP) work by changing `baseURL` and API key.
- No‑code (n8n, Zapier): set `https://api.paywalls.ai/v1` in AI nodes; use credentials for the key. Custom HTTP requests also supported.
- MCP tools: charge per tool invocation with `POST /v1/user/charge`; return the assistant message if funds/authorization are needed.
- Vercel AI SDK: use `OpenAIStream` with the Paywalls client; deploy at the edge.

## Pricing & metering

- Per‑token with per‑model prices; hybrid minimums keep margins predictable.
- Manual one‑off charges for tools/actions, layered on top of token metering.
- Configure pricing per model; analytics show revenue, cost, and profit.

## Analytics & reporting

- Real‑time ledger of deposits, charges, refunds, and adjustments.
- Users view with balances, spend, and activity.
- Charts for revenue, provider cost (COGS), and profit; model mix and top users.

## Security & compliance

- Keep keys server‑side; use restricted provider keys (e.g., Stripe Restricted Key).
- Prefer pseudonymous user ids; avoid PII in ids and metadata.
- Verify webhooks using raw bodies; make handlers idempotent.

---

## Docs

- Quickstart: `quickstart.mdx`
- Pass user identity: `how-to-guides/pass-user-identity.mdx`
- No‑code flows: `how-to-guides/no-code-zapier-n8n-flows.mdx`
- MCP tools: `how-to-guides/monetize-mcp-tools.mdx`
- Monetization recipes: `how-to-guides/use-case-monetization-recipes.mdx`
- Core concepts: `core-concepts/user-identity.mdx`, `core-concepts/pricing-metering.mdx`, `core-concepts/paywalls-wallets-ledger.mdx`
- API reference: `api-reference/introduction.mdx`
- Reliability & security: `more/errors-limits-reliability.mdx`, `more/security-compliance.mdx`
- Analytics: `more/analytics-reporting.mdx`
- Roadmap & changelog: `more/roadmap.mdx`, `more/changelog.mdx`

## Support

Email support@paywalls.ai or open an issue.
