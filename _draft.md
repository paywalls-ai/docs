# Paywalls.ai — AI‑first Monetization Platform (Docs Draft v5)

> **Definition**: Paywalls.ai is an **AI‑first monetization platform** for no‑code builders and developers alike. It’s not just another billing tool—it’s purpose‑built for AI apps, agents, and automations. Today it provides an OpenAI‑compatible **chat completions proxy** with real‑time metering, dynamic pricing, billing and payment integrations; next, it’s expanding to **images, audio, video, embeddings, MCP tools,** **agent‑to‑agent payments** and more.

**Base API URL:** `https://api.paywalls.ai/v1`

---

## Table of Contents

### **Getting started**

- Introduction
- Quickstart

### **Core concepts**

- Paywalls, wallets & ledger
- User identity
- Pricing & metering model
- Request lifecycle (high‑level)
- Model Providers
- Proxy

### **How‑to guides**

- Choose a mode (Default vs Shared)
- Configure billing
- Connect Stripe
- BYOK vs built‑in provider
- Shared mode (Cross‑app)
- Monetize MCP tools
- No‑code: Zapier / n8n flows
- Use‑case (monetization) recipes
- Use Paywalls with existing subscriptions (Stripe/PSP)

### **SDKs & Integrations**

- Client SDKs
- Front‑end widgets
- Vercel AI SDK
- LangChain / LlamaIndex
- MCP integrations

### **Testing & Sandbox**

- Test keys & environments
- Playground

### **Errors, limits & reliability**

### **Security & compliance**

### **Analytics & reporting**

### **Release notes**

- **Changelog**
- **Roadmap**

### **Glossary**

# Getting started

## Introduction

**Build AI products, not a billing system.**
Paywalls.ai is an **AI‑first monetization platform** that lets you turn model usage into revenue—without hiring a second team to design pricing engines, ledgers, wallets, webhooks, or dunning flows. You focus on your product; we handle the hard parts of **pricing, metering, and getting paid**.

### What you get (at a glance)

- **Monetization in minutes, not months** — launch with a working checkout, and usage metering instead of building billing, reconciliation, and reporting from scratch.
- **Dynamic, AI‑native pricing** — price per request, per token, or hybrid; set different rates per model or feature; run promos or free/trial credits without code changes.
- **Instant paywalls** — block unpaid usage and surface prebuilt **authorize/top‑up** experiences exactly when needed so you stop leaking value.
- **Wallets that fit your go‑to‑market** — pick **Default (app‑scoped)** balances for a tightly controlled UX or **Shared (cross‑app)** balances when you want users to top up once and spend across apps.
- **Provider‑agnostic control** — standardize pricing across OpenAI/Anthropic/other providers and keep margins stable even as model menus and rates evolve.
- **A real ledger** — every top‑up and charge is recorded for exports, audits, and analytics.

### Why this matters

- **AI usage is spiky and granular.** Prices differ by model, input/output tokens, and even capability tiers. Rolling your own metering and price logic is slow, brittle, and distracts from shipping features. Paywalls.ai gives you a **purpose‑built, usage‑based foundation** that matches how AI is actually billed.
- **Pricing changes often.** You’ll want to experiment (e.g., minimum fee + per‑token, per‑tool charges, trials, bundles). With Paywalls.ai, you can **iterate pricing without refactoring** your app.
- **Cash flow and guardrails.** Built‑in balance checks and spend controls stop runaway costs, while a clear ledger makes finance and support easier.

### Common outcomes for builders

- **Launch paid v1 fast:** gate AI usage behind a paywall and start charging in minutes — no custom billing stack.
- **Freemium that converts:** give, for example, **100 free messages** or **\$5 in trial credits**, then automatically prompt for top‑ups or route to a subscription.
- **Predictable margins:** enforce a **minimum fee** plus **per‑token** pricing per model; change rates centrally without code changes.
- **Per‑model dynamic pricing:** let users pick a model and automatically apply the right rate while protecting margins across providers.
- **Scale from no‑code to prod:** start with no‑code tools like Zapier/n8n; graduate to Node/Python/Edge using the same paywall settings.

### What Paywalls.ai replaces

- **Metering hacks:** ad‑hoc token counters, cron jobs, and spreadsheet‑driven charge calculations.
- **Patchwork payments:** one‑off Stripe calls/webhooks scattered across services; manual reconciliation.
- **DIY ledgers:** custom tables/scripts for balances, top‑ups, refunds, and disputes.
- **Inconsistent paywalls:** scattered “pay now” modals and error messages that don’t reflect real balance/authorization state.

### What stays yours

- Your product experience, data flows, and model choices.
- Your pricing strategy — Paywalls.ai makes it easy to implement and change.

**Next steps**
Head to **Quickstart** to make your first paid request. When you’re ready to go deeper, see **Core concepts** for paywall modes, user identity, and pricing, then explore **How‑to guides** for Stripe, model providers, MCP charges, and no‑code flows.

## Quickstart

### Step 1 — Create a paywall & choose mode (immutable)

Create a paywall in the dashboard and choose your mode. This sets how money flows and **cannot be changed later**.

- **Default (App‑scoped wallet)** — You collect payments directly (e.g., Stripe or your own rails). Users’ balances live in your app’s scope. You credit balances via **Deposit API** and charges deduct from that balance. _No end‑user authorization step._
- **Shared (Cross‑app wallet)** — Paywalls.ai hosts a **user‑controlled** wallet that can be spent across apps. Users authorize your paywall. You earn per usage and later **withdraw**. _End‑user authorization required._

> Not sure which to pick? See **How‑to → Choose a mode (Default vs Shared)**.

---

### Step 2 — Configure a model provider

- **Default mode (required):** connect a provider via **BYOK**. Options include **OpenAI**, **Together AI** (OpenAI‑compatible), **OpenRouter** (unified OpenAI‑style API), or any **OpenAI‑compatible** endpoint (self‑hosted or vendor).

  > In Default mode, balances are _virtual/app‑scoped_, so Paywalls cannot pay a third‑party model provider on the user’s behalf.

- **Shared mode (optional):** either use the **built‑in provider (powered by OpenRouter)** with **no setup** to access OpenRouter models immediately, or connect your own keys (BYOK). Model costs are charged to the **end‑user’s shared wallet**; Paywalls automatically splits charges to preserve your developer margin.

---

### Step 3 — Configure payments / top‑ups

- **Default mode:** in the dashboard, paste a **restricted Stripe API key**. Paywalls will **auto‑generate checkout links** when users need to top up and **auto‑subscribe to webhooks** to credit balances. _(No custom checkout/webhook code required.)_
  Alternatively, use **custom rails** and call **Deposit API** after you receive funds.
- **Shared mode:** no payment setup required—**Paywalls hosts top‑ups**. Users fund their shared wallet; you’ll see earnings accrue for **withdrawal**.

---

### Step 4 — Copy API key & base URL; make your first request

Grab your paywall API key (`sk-paywalls-…`) and point your existing OpenAI client at the Paywalls base URL.

**Node (OpenAI SDK)**

```ts
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.PAYWALLS_API_KEY, // sk-paywalls-...
  baseURL: "https://api.paywalls.ai/v1",
});

const resp = await client.chat.completions.create({
  model: "openai/gpt-4o-mini",
  user: "user_123", // or: send X-Paywall-User header
  messages: [{ role: "user", content: "Hello!" }],
});
console.log(resp.choices[0]?.message?.content);
```

**Python (OpenAI SDK)**

```python
from openai import OpenAI
import os

client = OpenAI(api_key=os.environ["PAYWALLS_API_KEY"],
                base_url="https://api.paywalls.ai/v1")

resp = client.chat.completions.create(
    model="gpt-4o-mini",
    user="user_123",
    messages=[{"role": "user", "content": "Hello!"}],
)
print(resp.choices[0].message.content)
```

**fetch (header identity)**

```js
const r = await fetch("https://api.paywalls.ai/v1/chat/completions", {
  method: "POST",
  headers: {
    Authorization: `Bearer ${process.env.PAYWALLS_API_KEY}`,
    "Content-Type": "application/json",
    "X-Paywall-User": "user_123", // header-based identity
  },
  body: JSON.stringify({
    model: "openai/gpt-4o-mini",
    messages: [{ role: "user", content: "Hello!" }],
  }),
});
const json = await r.json();
```

**curl**

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

---

### Step 5 — How responses behave (both modes)

- Paywall-required flows arrive as **normal assistant messages**. Your app does **not** need special branching—render them like any chat reply.
- If the user must **authorize** (Shared) or **top up** (both modes), the assistant message already contains the **link** (authorization or checkout). After completion, usage proceeds and the next responses stream normally.
- **Default mode**: Stripe webhooks **auto‑deposit** into the app‑scoped balance.
- **Shared mode**: charges deduct from the **shared wallet**; your earnings accrue for **withdrawal**.

> **Tip**: Always send a stable, pseudonymous `user` value—either in the body (`user`) or via `X-Paywall-User`.

---

# Core concepts

## Paywalls, wallets & ledger

A **Paywall** is a named, versioned configuration that defines **how usage turns into money** for a given product or assistant. It binds together:

- **Mode** _(Default or Shared)_ — where balances live and who controls funds.
- **Pricing rules** — per‑request, per‑token, hybrid, and optional free credits.
- **Provider access** — BYOK keys and routing, or the built‑in provider (Shared mode).
- **Payment rails** — automated Stripe checkout (Default) or hosted top‑ups (Shared).
- **Runtime rules** — spend caps, token caps, concurrency, and rate limits.

### Wallets

- **Default (App‑scoped wallet)** — Virtual credits live in your app’s scope. You decide when/how to top up (Stripe or custom rails), and charges deduct from this balance. Best for single‑app products, B2B contracts, and cases where you own refund policies.
- **Shared (Cross‑app wallet)** — Real funds live in a user‑controlled wallet that works across any Paywalls‑enabled app. Users authorize your paywall, top up once, and spend anywhere. Best for ecosystems and marketplaces.

### Ledger

Every monetary event becomes a **ledger entry** (immutable). Typical entry types:

- `topup` _(user‑initiated)_ — funds added to a shared wallet (hosted) or your Default checkout.
- `deposit` _(developer‑initiated, Default)_ — credit a user after your custom PSP confirms payment.
- `charge` — usage‑based deduction linked to a specific request (`request_id`, model, token stats).
- `adjustment` / `refund` — corrections or goodwill credits.

Each entry records **timestamp, paywall id, user id, model, pricing rule, currency/amount, metadata**. Use ledger exports for support, finance, and analytics.

### Money flow (by mode)

- **Default:** End‑user pays **you** (Stripe/custom). You pay the model provider via **BYOK**. Paywalls meters, enforces pricing, and writes the ledger.
- **Shared:** End‑user funds their **shared wallet**. Paywalls settles the **provider cost** (built‑in provider or BYOK) and credits your **developer earnings**; you **withdraw** later.

### Limits & controls

Apply **per‑user** or **per‑paywall** controls: daily spend caps, per‑request token caps, and concurrency caps. These guard your margins and keep UX predictable.

> **TODO:** Publish fee schedule, withdrawal rules, and on‑chain details.

## User identity

Every billable call **must include a stable user id** so we can authorize, meter, and bill the right account.

**What we use it for**

- Authorization & balance checks (Shared: access must be granted; Default: evaluate balance).
- Per‑user limits (spend caps, token caps, concurrency).
- Correct ledger attribution and analytics.

**Design a good **\`\`** id**

- Prefer **pseudonymous**, stable identifiers (e.g., `user_abc123`). Avoid PII (emails/phone numbers).
- One logical person/device → one stable id across your app(s). If you migrate auth systems, add a mapping layer instead of changing ids in requests.
- If you must rotate ids, store the old id in your own mapping; continue sending a **stable** id to Paywalls.

**Where to send it**

1. **Body** `user` _(recommended; works with OpenAI SDKs)_
2. **Header** `X-Paywall-User` _(easy via middleware)_
3. **URL prefix** `/{user}/...` _(fallback when you can’t modify body/headers)_

**Common mistakes**

- Using different ids on different requests for the same person.
- Sending PII as the id.
- Omitting the id on streaming retries (breaks metering/charges).

## Pricing & metering model

Pricing is **rule‑based** and **model‑aware** so you can balance UX and margins.

### Pricing layers

- **Per request** — a flat fee per API call (good for tools/actions and short prompts).
- **Per token** — meter **prompt** + **completion** tokens (finalized after generation).
- **Hybrid** — combine both (e.g., _minimum fee + per‑token_) to keep margins predictable.
- **Promos/credits** — trial credits to reduce onboarding friction. _(TODO: expiry & abuse protection in dashboard.)_

### Per‑model dynamic pricing

Set different rates per model so users can self‑select price/performance. Hybrid typically provides the most stable margin across varied prompt sizes.

### Metering details

- Token usage is computed per request. When streaming, totals are **finalized at end** of the stream.
- Charges round to the smallest supported unit for the configured currency. _(TODO: document precision & rounding rules.)_
- Manual charges (e.g., per‑tool fees) can be combined with usage charges in the same ledger period.

## Request lifecycle (high‑level)

1. **Request** — Your app calls the OpenAI‑compatible endpoint with a **paywall key** and a \`\`\*\* id\*\*.
2. **Decision** — The platform evaluates **authorization** (Shared), **balance**, **limits**, and **pricing** rules.
3. **If action is needed** — The response is a normal **assistant message** containing an **authorization** or **checkout** link. You render it as‑is; no special branching.
4. **Execution** — If authorized & funded, the request is forwarded to the configured **provider** (BYOK or built‑in). Streaming is passed through.
5. **Meter & bill** — Token usage is measured; the applicable pricing rule computes the charge. A **charge** entry is written to the **ledger**.
6. **Events & analytics** — Webhooks (optional) and exports keep your systems in sync. _(TODO: event names & payloads.)_

> **TODO:** Add sequence diagram and a small state machine sketch.

## Model Providers

Two integration styles:

- **BYOK (bring your own key)** — Connect providers you already use (OpenAI, Together, OpenRouter, or any OpenAI‑compatible endpoint). **Required for Default mode.** You pay your provider; Paywalls bills your users.
- **Built‑in provider (Shared mode)** — Use the built‑in provider (powered by OpenRouter) without adding keys. The end user’s shared wallet covers provider cost; Paywalls splits the charge to preserve your margin. _(Roadmap: allow built‑in provider with Default mode; developer pays provider separately.)_

**Model selection & routing**

- You choose a model id (e.g., `openai/gpt-4o-mini`, `openrouter/llama-3.1-70b`). We normalize requests and route to the right upstream.
- You can mix providers across requests while keeping one pricing surface.
- _(Optional)_ Fallbacks and failover depend on provider capabilities. **TODO:** Document policies per provider.

See the separate **API Reference (Models)** for listing and selecting models.

## Proxy

The **proxy** is the drop‑in, OpenAI‑compatible layer that **enforces pricing and authorization**, **meters usage**, and **returns paywall‑aware assistant messages**. It dramatically simplifies setup because you **don’t need to build billing branches** in your app.

### Why use the proxy?

- **Zero‑branching UX** — When a user must authorize or top up, the proxy replies with a **normal assistant message** containing the correct link. You just render what you get.
- **One client surface** — Keep using OpenAI SDKs and tools; change only the **base URL** and **key**.
- **Consistent metering** — Requests are normalized, streamed tokens are measured, and pricing is applied centrally.
- **Provider flexibility** — Swap models/providers without rewriting your app or pricing logic.
- **Central guardrails** — Spend caps, token caps, and rate limits enforce your rules before provider calls.

### What the proxy does

- **Compatibility** — Accepts OpenAI‑style Chat Completions (incl. streaming, function/tool calls, JSON modes). _(Modalities beyond text are roadmap‑dependent.)_
- **Decisioning** — Checks authorization (Shared), balance, and limits; decides whether to **return a link** or **execute** the request.
- **Routing** — Forwards eligible requests to the selected provider (BYOK or built‑in) and proxies the stream back to you.
- **Metering & pricing** — Counts tokens, computes charges, and writes **ledger** entries.
- **Observability** — Attaches a **request id** and correlates ledger entries and events for support/analytics.

### Security expectations

- Your **paywall API key** must remain **server‑side**.
- Always include a stable `user` id to prevent cross‑account leakage and enable correct limits.
- Use **idempotency keys** for your own side‑effecting calls (e.g., manual charges) to avoid duplicates.

### Performance & limits

- Streaming is proxied with minimal overhead; if a stream drops, clients may reconnect and retry safely.
- Rate limits and concurrency caps protect your app and budget. _(TODO: publish exact limits.)_

> **Optional but recommended:** You can call Paywalls APIs directly for non‑chat features (e.g., manual charges). For chat usage, the proxy gives you the fastest path to a working, billable flow without custom billing code.

---

# How‑to guides

## Choose a mode (Default vs Shared)

Choosing a mode defines how money flows and **can’t be changed later**. Use this decision guide to pick the right setup for your product and team.

### 60‑second decision quickstart

- **Pick Default (App‑scoped)** if you want funds to land in **your Stripe or custom rails**, need tight control over refunds, and can connect your own **model provider** (BYOK).
- **Pick Shared (Cross‑app)** if you want a **built‑in model provider** (powered by OpenRouter) with **no setup**, users to **top up once and spend across apps**, and built‑in **revenue split** that preserves your margin.

### Comparison

| Criterion                       | Default (App‑scoped wallet)                                                           | Shared (Cross‑app wallet)                                                     |
| ------------------------------- | ------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| **Who holds funds**             | Developer (virtual, app‑scoped balance)                                               | End user (shared wallet across apps)                                          |
| **Who pays the model provider** | **Developer** (BYOK required)                                                         | **Paywalls (built‑in) or BYOK**; end user pays from shared wallet             |
| **Provider setup**              | **Required**: connect OpenAI, Together, OpenRouter, or any OpenAI‑compatible endpoint | **Optional**: built‑in provider available with no setup; BYOK also supported  |
| **End‑user authorization**      | Not required                                                                          | Required (user authorizes your paywall)                                       |
| **Top‑ups / payments**          | Your Stripe (restricted key) or custom rails; auto‑checkout link + auto‑deposit       | Hosted by Paywalls; link provided in assistant message                        |
| **Cash flow**                   | Funds settle to **your accounts** immediately                                         | Earnings accrue in your **paywall**; withdraw later                           |
| **Refunds**                     | You control refunds/credits                                                           | Refunds handled via shared wallet / platform rules _(TODO: finalize details)_ |
| **Portability**                 | Balance usable **only** in your app                                                   | Balance usable **across** Paywalls‑enabled apps                               |
| **Model catalog**               | Whatever your BYOK provides                                                           | Full OpenRouter catalog via built‑in provider (plus BYOK if desired)          |
| **Setup time**                  | Minutes (provider + payments)                                                         | Seconds (payments hosted; provider optional)                                  |
| **Can switch later?**           | **No**                                                                                | **No**                                                                        |

### Use this if…

- **Default:** You need direct cash flow to your Stripe, custom pricing per model you contract directly, or B2B control (credits, invoices, refunds) under your policies.
- **Shared:** You want a low‑friction launch, cross‑app user wallets, and automatic provider settlement that preserves your configured margin.

### Avoid if…

- **Default:** You don’t want to manage a provider relationship or payments yourself.
- **Shared:** You need per‑customer invoice workflows under your own merchant account.

### After you choose

- **Default:** Go to **Quickstart → Step 2 (Configure a model provider)**, then **Step 3 (Payments / top‑ups)**, then **Step 4 (First request)**.
- **Shared:** Go to **Quickstart → Step 2 (Provider: optional)** and **Step 4 (First request)**; payments/top‑ups are hosted for you.

> See also: **Core concepts → Paywalls, wallets & ledger**, **Core concepts → Model Providers**, **How‑to → BYOK vs built‑in provider**, **How‑to → Connect Stripe**.

## Configure billing

Design billing that matches AI usage and preserves your margins. You can change pricing and rules in the dashboard at any time without touching your app code. You can use **one** method or **combine** them.

### 1) Deposit credits via **Deposit API** _(Default mode only)_

**What it does:** Credit a user’s **app‑scoped** wallet with an arbitrary amount.

**Use cases**

- Integrate **any payment system** (PSP, crypto, app store) — after you confirm payment, deposit credits.
- Grant **trial credits** to new users or cohorts.
- Add credits at the **start of a billing cycle** or for **goodwill/refunds**.

**Notes**

- Appears as a **`deposit`** in the ledger (developer‑initiated).
- Works only in **Default mode** (virtual, app‑scoped balance).
- Pair with the automated **Stripe restricted key** setup for a zero‑code top‑up flow.

### 2) One‑off charges via **Charge API** _(Both modes)_

**What it does:** Create a **manual, one‑time charge** independent of token usage.

**Use cases**

- Bill for **tool usage** (e.g., MCP tool run, file conversion, image upscaling).
- Charge for **feature access** (e.g., unlock premium mode or export).
- Apply **post‑processing** fees (e.g., retrieval/storage, long‑running jobs).

**Notes**

- Appears as a **`charge`** in the ledger.
- If funds/authorization are missing, the API returns a **renderable message** (auth/top‑up link); show it as a normal assistant reply.
- Use an **Idempotency‑Key** to avoid duplicates; include **metadata** for reconciliation.

### 3) Automatic usage billing via the **Proxy** _(Both modes)_

**What it does:** The proxy **meters tokens** and computes the charge from the **selected model price** + your **markup**. It writes the charge to the ledger and returns the model response (or an auth/top‑up message) — no custom billing code required.

**Setup**

- **Connect a provider**: BYOK (Default required; Shared optional) or the **built‑in provider** (Shared).
- **Set per‑model prices** and an optional **markup %** in the dashboard.

**Behavior**

- Charge = _usage (prompt + completion tokens) × model price_ **±** _markup_.
- Per‑model pricing lets users pick price/performance while you keep margins predictable.
- Changes take effect **without code changes**.

### 4) Per‑request & hybrid pricing _(Coming soon)_

Configure fixed **per‑request** fees, or **hybrid** pricing: _minimum fee per request + per‑token usage_. Useful for short prompts, tools/actions, and stabilizing margins across mixed workloads.

---

### Recommendations & guardrails

- Prefer **per‑model dynamic pricing** and set a **minimum per‑request** fee to cover short prompts.
- Add **max tokens** and **spend caps** to prevent runaway costs.
- Keep pricing rules in the dashboard; don’t hardcode rates in your app.
- For UI display, read prices/models from the separate **API Reference (Models)**.

## Connect Stripe (Default mode)

In Default mode you collect payments directly. The fastest path is the **automated Stripe integration**; advanced teams can still bring custom rails.

### Fast path — Automated Stripe integration

1. In the Paywalls dashboard, paste a **restricted Stripe API key**.
2. Paywalls will **auto‑create checkout links** when a user needs to top up and **auto‑subscribe to webhooks** to deposit credits after successful payment.
3. The **assistant message** returned to your user includes the **checkout link**—render it like any model reply. After payment, the user’s **app‑scoped balance** is credited automatically.

### Advanced — Custom rails

- Use your own checkout (Stripe Elements/Checkout or another PSP). On payment success, call `POST /v1/user/balance/deposit` to credit the user.
- Keep your internal `user_id`, PSP `payment_intent`, and any invoice refs in `metadata` for reconciliation.

> Security: Use a **restricted** key with only the permissions Paywalls needs; keep all API keys server‑side.

## BYOK vs built‑in provider

### Default mode (App‑scoped)

- **Required:** Connect a **model provider** via BYOK. Paywalls cannot charge a third‑party provider directly from the app‑scoped virtual wallet.
- **Options:** OpenAI, Together AI (OpenAI‑compatible), OpenRouter (unified OpenAI‑style API), or any OpenAI‑compatible endpoint.
- **Billing:** You pay your provider; Paywalls bills your **end users** against their app‑scoped balance.

### Shared mode (Cross‑app)

- **Optional:** Use the **built‑in provider (powered by OpenRouter)** to run any supported model with **no setup**.
- **Alternative:** BYOK is also supported if you need your own contracts or models.
- **Billing:** End users pay from their **shared wallet**; Paywalls automatically splits the charge between **developer revenue** and **provider cost** to preserve your configured margin.

> **Roadmap:** Built‑in provider for **Default mode** (developer pays the provider separately; Paywalls still handles billing your users).

## Use Paywalls with existing subscriptions (Stripe/PSP)

You can keep your **existing subscription plans** (e.g., monthly tiers in Stripe) and use Paywalls to **meter AI usage**. The pattern is simple:

- When a subscription cycle is **paid**, grant the user **credits** by calling the **Deposit API** (Default mode only).
- As the user consumes the product, the **Proxy** charges usage against that credit balance.
- If the balance reaches zero **mid‑cycle**, users can **top up** via the automated Stripe Checkout flow to continue using the service immediately.
- At the **next cycle**, grant a **new bundle of credits** according to the plan. (Decide whether unused credits roll over.)

> This guide targets **Default mode** (app‑scoped wallet). In **Shared mode**, the wallet is user‑controlled and external deposit grants aren’t supported yet. You can still run subscriptions for access/entitlements while usage is billed from the shared wallet.

### Map plans → credit grants

Create a table that maps subscription **tiers** to the **credit amount** you want to deposit each cycle, for example:

| Plan    | Monthly price |  Monthly credits |
| ------- | ------------: | ---------------: |
| Starter |           \$9 |    90,000 tokens |
| Pro     |          \$29 |   400,000 tokens |
| Team    |          \$99 | 2,000,000 tokens |

> Choose units that match your pricing (**tokens**, **requests**, or **hybrid**). You can change the mapping any time; the next cycle uses the new amounts.

### Event to listen for (Stripe)

Listen for the subscription invoice being **paid** and only then grant credits:

- `invoice.payment_succeeded` with `billing_reason=subscription_create` → first cycle
- `invoice.payment_succeeded` with `billing_reason=subscription_cycle` → renewals

These values ensure you don’t grant credits for proration or non‑subscription invoices.

### Pseudocode (webhook → Deposit API)

```ts
// 1) Receive Stripe webhook
if (event.type === "invoice.payment_succeeded") {
  const invoice = event.data.object;
  const reason = invoice.billing_reason; // 'subscription_create' | 'subscription_cycle' | ...
  if (reason === "subscription_create" || reason === "subscription_cycle") {
    const userId = mapStripeCustomerToUser(invoice.customer);
    const plan = extractPlanFromInvoice(invoice);
    const creditAmount = creditsForPlan(plan); // your mapping

    // 2) Grant credits in Default mode
    await fetch("https://api.paywalls.ai/v1/user/balance/deposit", {
      method: "POST",
      headers: {
        Authorization: `Bearer ${process.env.PAYWALLS_API_KEY}`,
        "Content-Type": "application/json",
        "Idempotency-Key": invoice.id, // prevent double deposits on retries
      },
      body: JSON.stringify({
        user: userId,
        amount: creditAmount,
        metadata: { plan, invoice: invoice.id },
      }),
    });
  }
}
```

**Notes**

- Always make the handler **idempotent** using a stable key (e.g., `invoice.id`).
- If you support **upgrades/downgrades** mid‑cycle, decide whether to grant a **pro‑rated** deposit or wait until the next cycle.
- Record your mapping and resulting deposits in your own DB for support/audit.

### Mid‑cycle top‑ups (optional)

If a user burns through their plan credits before renewal:

- The Proxy will return a **normal assistant message** with a **Stripe Checkout** link.
- After payment succeeds, Paywalls **auto‑deposits** the purchased credits.
- The user can continue immediately; the next cycle still grants the plan’s credits.

### Other payment systems (PSPs)

If you’re not on Stripe (or you also sell through other channels like app stores or crypto):

- Handle the **payment confirmation** in your system.
- Call the **Deposit API** with the correct amount and metadata (transaction id, plan, etc.).
- Keep the same idempotency and audit practices.

### Testing

- Use **Stripe test keys** and **test cards** to run through the full flow in **Default mode** (no real funds).
- Verify deposits appear in the **ledger** and usage charges decrement the balance.
- Switch to live by replacing the Dashboard key with your **restricted live key**.

**FAQ**

- **Do credits roll over?** Your choice. If not, reset at each cycle; if yes, deposit only the delta.
- **What if the subscription payment fails?** Don’t deposit; the next successful payment triggers the grant.
- **Can I combine Subscription + pay‑as‑you‑go?** Yes—grant a monthly bundle, then let users top up mid‑cycle as needed.

## Monetize MCP tools (per‑tool charges & spend caps)

Use **manual charges** after/around tool calls:

```ts
import axios from "axios";
await axios.post(
  "https://api.paywalls.ai/v1/user/charge",
  { user: userId, amount: "0.001", metadata: { requestId, tool: toolKey } },
  {
    headers: {
      Authorization: `Bearer ${process.env.PAYWALLS_API_KEY}`,
      "Idempotency-Key": requestId,
    },
  }
);
```

On `{ success: false, message }`, render the message verbatim (it already contains auth/top‑up UI).

## No‑code: Zapier / n8n flows

Ship a paywalled AI flow with no code by calling the Chat Completions endpoint from your workflow tool. The **assistant message** you get back is what you show to users—no special branching required.

- **Zapier**: Use **Webhooks by Zapier → Custom Request** to call `POST https://api.paywalls.ai/v1/chat/completions` with your headers/body. The returned assistant message may include an **authorization** or **checkout** link—just pass it through to your UI or messaging step.
- **n8n**: Use the **HTTP Request** node with `Authorization: Bearer <PAYWALLS_API_KEY>` and optional `X-Paywall-User`. Forward the returned assistant message to the next node (chat UI, email, Slack, etc.).

> Tip: Store your `PAYWALLS_API_KEY` as a secret/credential in your no‑code tool; do not hardcode it in workflows.

**TODO:** Official templates & screenshots.

## Use‑case recipes (AI monetization)

- Per‑token metering with minimum fee for chat.
- Charge per tool call with daily **spend cap**.
- Freemium trial → prepaid top‑ups → subscription + overage.
- Multi‑agent app with per‑model pricing.
  **TODO:** Full examples & starter snippets.

---

# SDKs & Integrations

## Client SDKs

**Client‑agnostic by design.** If your client can call an OpenAI‑style HTTP API, it works with Paywalls. In most cases you only change the \`\` and the **API key**—no proprietary SDK required.

**Works with:** official OpenAI SDKs (**Node, Python, .NET, Java**), community clients (**Go, Ruby, Rust, PHP**), and plain HTTP (**fetch/axios, curl, Postman**). Edge/serverless runtimes are supported.

**Compatibility surface:** Chat Completions (incl. streaming), tools/function‑calling, JSON/structured outputs. Identify the end user via body `user` (preferred) or header `X‑Paywall‑User`.

**Node (streaming)**

```ts
import OpenAI from "openai";
const client = new OpenAI({
  apiKey: process.env.PAYWALLS_API_KEY,
  baseURL: "https://api.paywalls.ai/v1",
});
const stream = await client.chat.completions.create({
  model: "openai/gpt-4o-mini",
  user: "user_123",
  stream: true,
  messages: [
    { role: "user", content: "Explain usage-based pricing in 2 lines." },
  ],
});
for await (const chunk of stream) {
  const delta = chunk.choices?.[0]?.delta?.content;
  if (delta) process.stdout.write(delta);
}
```

**Python (streaming)**

```python
from openai import OpenAI
import os

client = OpenAI(api_key=os.environ["PAYWALLS_API_KEY"], base_url="https://api.paywalls.ai/v1")

with client.chat.completions.create(
    model="openai/gpt-4o-mini",
    user="user_123",
    stream=True,
    messages=[{"role": "user", "content": "Explain usage-based pricing in 2 lines."}],
) as stream:
    for event in stream:
        delta = event.choices[0].delta.content if event.choices and event.choices[0].delta else None
        if delta:
            print(delta, end="")
```

**Go (streaming via **\`\`**)**

```go
package main
import (
  "context"
  "errors"
  "fmt"
  openai "github.com/sashabaranov/go-openai"
)
func main() {
  cfg := openai.DefaultConfig("$PAYWALLS_API_KEY")
  cfg.BaseURL = "https://api.paywalls.ai/v1"
  c := openai.NewClientWithConfig(cfg)

  req := openai.ChatCompletionRequest{
    Model:   "openai/gpt-4o-mini",
    Stream:  true,
    Messages: []openai.ChatCompletionMessage{{Role: openai.ChatMessageRoleUser, Content: "Explain usage-based pricing in 2 lines."}},
  }
  stream, err := c.CreateChatCompletionStream(context.Background(), req)
  if err != nil { panic(err) }
  defer stream.Close()

  for {
    resp, err := stream.Recv()
    if errors.Is(err, io.EOF) { break }
    if err != nil { panic(err) }
    fmt.Print(resp.Choices[0].Delta.Content)
  }
}
```

**cURL (streaming / SSE)**

```bash
curl -N https://api.paywalls.ai/v1/chat/completions \
  -H "Authorization: Bearer $PAYWALLS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "openai/gpt-4o-mini",
    "user": "user_123",
    "stream": true,
    "messages": [{"role":"user","content":"Explain usage-based pricing in 2 lines."}]
  }'
```

**.NET (HttpClient, non‑stream)**

```csharp
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Text.Json;

var http = new HttpClient();
var req = new HttpRequestMessage(HttpMethod.Post, "https://api.paywalls.ai/v1/chat/completions");
req.Headers.Authorization = new AuthenticationHeaderValue("Bearer", Environment.GetEnvironmentVariable("PAYWALLS_API_KEY"));
req.Content = new StringContent(JsonSerializer.Serialize(new {
  model = "openai/gpt-4o-mini",
  user = "user_123",
  messages = new[]{ new { role = "user", content = "Explain usage-based pricing in 2 lines." } }
}), Encoding.UTF8, "application/json");
var res = await http.SendAsync(req);
var json = await res.Content.ReadAsStringAsync();
Console.WriteLine(json);
```

> Notes: (1) Always send a stable, pseudonymous `user` id. (2) For browser-only apps, **do not** expose your paywall key—use a server, edge function, or proxy.

## Front‑end widgets (Paywalls UI)

🚧 **Roadmap — Front‑end widgets (in development)**
These will provide drop‑in components and hooks to embed **authorization**, **top‑up**, **balance**, and **activity** in your UI without custom paywall plumbing.

**What they will enable**

- Render paywall assistant messages (auth/top‑up) as native UI and handle link flows automatically.
- Show **BalanceBadge**, **AuthorizeButton**, **TopupButton**, and an **ActivityFeed** with minimal wiring.
- Expose hooks like `usePaywall`, `useBalance`, `useAuthorization` plus a context provider.
- Handle post‑checkout reconciliation (auto refresh/poll), loading/error states, and accessibility.
- Support **theming** and **i18n**; work with CSR/SSR.
- Keep your **paywall key server‑side** (widgets never require exposing it).

**Status:** Not yet available; actively in development.&#x20;

## Vercel AI SDK

Use `OpenAIStream` with the Paywalls baseURL to stream responses at the edge. Example:

```ts
import OpenAI from "openai";
import { OpenAIStream, StreamingTextResponse } from "ai";
export const runtime = "edge";
export async function POST(req: Request) {
  const { messages } = await req.json();
  const openai = new OpenAI({
    apiKey: process.env.PAYWALLS_API_KEY!,
    baseURL: "https://api.paywalls.ai/v1",
  });
  const response = await openai.chat.completions.create({
    model: "openai/gpt-4o-mini",
    user: "user_123",
    stream: true,
    messages,
  });
  const stream = OpenAIStream(response);
  return new StreamingTextResponse(stream);
}
```

---

# Testing & Sandbox

## Test keys & environments

Paywalls.ai does not yet provide a platform‑wide **sandbox/test mode**. You can still run safe end‑to‑end tests in **Default mode** using **Stripe test keys** (no real funds used), then switch to live keys for production.

### Default mode — test with Stripe

1. In the dashboard, paste a **restricted Stripe \*\*\***test**\*** API key\*\* (`sk_test_…`).
2. Trigger a top‑up normally. The assistant message will include a **Stripe test checkout** link.
3. Pay with a **Stripe test card** (e.g., `4242 4242 4242 4242`). Paywalls receives the webhook and **auto‑deposits** credits to the user’s app‑scoped balance.
4. Make chat requests; charges are computed and written to the **ledger** just like production.

### Switch to live

- Replace the key with your **restricted Stripe \*\*\***live**\*** key** (`sk_live_…`) in the dashboard. **No code changes required.\*\*
- If you mirror balances in your own DB, clear any **test** balances/receipts before go‑live. Paywalls’ ledger will keep historical test entries for audit.

### Shared mode — current limitations

- Top‑ups are **hosted by Paywalls**; there is **no sandbox** yet. You can test **authorization** flows and paywall messages, but funding the shared wallet currently requires real payments. _(Roadmap: shared‑mode sandbox.)_

### Tips

- Never expose API keys in the browser; keep them server‑side.
- Use clearly labeled test users (e.g., `user_test_alice`) to filter analytics.
- If you use custom rails (non‑Stripe) in Default mode, simulate payments by calling **Deposit API** from your staging system.

## Playground

A simplified, hosted chat UI wired to your paywall. It lets you try things out without building your own app or UI. The Playground provides a basic chat interface, a **model selector**, and it **automatically handles authorization, top‑ups, and streaming responses**.

**Why it’s useful**

- Quickly validate pricing rules, limits, and balance behavior.
- Test BYOK vs built‑in provider routing with a model picker.
- Share with teammates/testers to gather feedback before wiring your own UI.

**How to access**

- Open your paywall in the dashboard and click **Playground**.
- Use the **unique shareable URL** to invite testers or users.

---

# Errors, limits & reliability

Design for failures you don’t control: networks, providers, retries, and user actions. These conventions keep requests safe and predictable.

## Idempotency

- Use an `Idempotency-Key` header for **non‑idempotent POSTs** such as `POST /v1/user/charge` (manual charges) or any custom side effects.
- Generate a unique key per logical operation (e.g., `requestId` or a UUID). If you retry, **reuse the same key** to prevent duplicate charges. Keys are typically retained for around 24 hours.

## Retries & backoff

- For **429/5xx** from providers, use **exponential backoff** with jitter.
- Webhooks are **at‑least‑once**: make handlers idempotent and safe to retry.

## Webhooks (verification & delivery)

- Verify signatures and use the **raw request body** during validation; many frameworks mutate bodies by default.
- Use a **tolerance window** to prevent replays and respond with `2xx` only after durable processing.

## Streaming resilience

- If a stream is interrupted, you can **retry the whole request** (idempotently) or show the partial transcript and let the user continue.
- Buffer streamed text on the server if you need to store a complete copy alongside the live stream.

## Rate limits

- Treat `429` as a signal to slow down; back off and optionally **queue** non‑interactive work.

## Timeouts

- Set request timeouts to avoid stuck connections; prefer **cancellable** streams in clients and servers.

## Observability

- Log `user`, `requestId`, and paywall **event ids** with your application logs so support and finance can reconcile quickly.

---

# Security & compliance

- Keep your **paywall key** server‑side; rotate if compromised.
- Prefer **pseudonymous** `user` IDs; avoid PII.
- Payments via Stripe/crypto handle PCI within their flows; your app must comply with its own ToS/privacy/tax obligations.
- **Data retention & audit — TODO:** retention periods, access controls, exports, regional hosting.

---

# Analytics & reporting

- **Ledger**: export charges, top‑ups, deposits with user, model, paywall dimensions.
- **Margin math**: developer revenue − provider costs − platform fees = net.
- **Recipes**: “top spenders last 7d”, “ARPU by model”, “profit by paywall”.
  **TODO:** CSV/Parquet export endpoints; dashboard screenshots.

---

# Release notes

## Changelog

> Placeholder — to be generated from releases. Include date, change summary, migration notes.

## Versioning & deprecations policy

- **API versioning** policy, sunset timelines, and deprecation emails.
- **Widget/SDK** version alignment and breaking‑change guidance.
  **TODO:** Formal policy text & examples.

---

# Glossary

Key terms used across these docs (kept short for scanning):

- **Paywall** — Configuration that enforces pricing, model/provider access, and payment logic for an app or assistant.
- **Mode** — Where wallets live and who controls funds: **Default** (app‑scoped) vs **Shared** (cross‑app).
- **Wallet** — Where a user’s balance is stored (real funds or credits), scoped by mode.
- **Ledger** — Record of charges, top‑ups, deposits.
- **Proxy** — The enforcement/metering layer that preserves OpenAI compatibility.
- **Model Registry** — List of available models with pricing & metadata.
- **Metering** — Measuring token usage (prompt + completion) per request for accurate charges.
- **Charge** — Deduction from balance for a specific request or tool call.
- **Deposit** — Developer‑initiated credit in default mode.
- **Top‑up** — User‑initiated addition of real funds/credits.
- **Authorization** — User’s grant that allows your paywall to spend (shared mode).

---

# Roadmap (public highlights)

- **Now**: OpenAI‑compatible chat proxy, model registry, manual charges, hosted payments, BYOK.
- **Next**: Images, audio, video, embeddings; MCP‑native helpers; UI widgets; on‑chain shared wallets; subscriptions; enhanced analytics.

**Questions?** Open an issue or reach out via support.&#x20;
