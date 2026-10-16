# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Public documentation site for **Hapuppy**, an OpenAI-compatible AI API gateway. It is a [Mintlify](https://mintlify.com) site — there is no application code, no build step beyond Mintlify's, and no test suite. Content lives in `.mdx` files; structure and theming live in `docs.json`.

Mintlify deploys automatically on push to `main` (no CI config in-repo). There is nothing to build or compile locally.

## Commands

```bash
# Local preview (hot-reloads .mdx and docs.json)
npx mintlify dev

# Validate links across the site before pushing
npx mintlify broken-links
```

## Architecture

### `docs.json` is the source of truth for navigation

Every page must be registered under `navigation.tabs[].groups[].pages` in `docs.json`. Creating an `.mdx` file alone does **not** make it appear in the sidebar. The site has exactly two tabs:

- **Guides** — `index.mdx`, `quickstart.mdx`, and everything under `guides/`
- **API Reference** — endpoint pages under `api-reference/<category>/<endpoint>.mdx`

Page paths in `docs.json` are written without the `.mdx` extension and without a leading slash (e.g. `"guides/authentication"`, `"api-reference/chat/completions"`).

### Files kept in-repo but currently hidden from navigation

Two groups of pages are intentionally not listed in `docs.json` but the source files are preserved so they can be re-enabled later:

- `api-reference/videos/*.mdx` — Video endpoints (Create / List / Get / Download / Delete). The product does not yet ship a video API.
- `guides/errors.mdx` — the error-handling guide. References to it from other pages were also removed.
- `guides/claude-apply.mdx` and `zh/guides/claude-apply.mdx` — the retired Claude application flow. Claude no longer needs an application and there is no role-play (RP) restriction; links to these pages were removed.

If you re-enable these, also restore the navigation entries and the cross-page links that were stripped. Conversely, when shipping new product surface, follow the same pattern (write the `.mdx`, add to `docs.json`) — do not delete dropped pages outright.

### API base URL: `https://api.hapuppy.com/v1`

All endpoint pages, code examples, and SDK snippets use `api.hapuppy.com`. `beta.hapuppy.com` is retired — do **not** reintroduce that hostname.

### Dashboard / billing / models links: three canonical URLs

The product website uses distinct paths for distinct intents. Match the intent to the URL:

| Intent | URL | Typical anchor text |
|---|---|---|
| "Go look at the dashboard" (settings, balance, API keys) | `https://hapuppy.com/en/dashboard` | `dashboard` / `Hapuppy Dashboard` |
| "Go buy a credit pack" | `https://hapuppy.com/en/dashboard/billing` | **`Credits Packs`** (note the exact casing) |
| "Browse the model catalog with multipliers" | `https://hapuppy.com/en/dashboard/models` | `Models` |

The phrase **"Credits Packs"** is the agreed-upon anchor text whenever the link is about purchasing — do not paraphrase to "Credits & Pricing", "Credit Pack", etc.

### Pricing model: one-time credit packs, no subscriptions, no free tier

A prior commit (`5b80786 fix: 修正定价描述与官网一次性购买模式对齐`) specifically corrected pricing copy to match the website. Whenever you touch pricing language:

- Credit packs are **one-time purchases**, not subscriptions. Lite $9.99 / Standard $19.99 / Pro $36.99.
- Purchased credits live in the **vault** and **never expire**.
- There is **no free tier**, no sign-up credits, and no daily reset.
- There are **no plan-based model permissions**: any paid account can call every model.
- Rate limit for every account: **100 RPM**.
- Payments are processed via **Creem**.
- Do not hardcode a model count ("100+"); point to the live [Models](https://hapuppy.com/en/dashboard/models) page or the List Models endpoint.

### Chat models are billed by separate input/output multipliers

Both `guides/credits.mdx` and any examples that quote a credit calculation use:

```
credits = prompt_tokens × input_multiplier + completion_tokens × output_multiplier
```

Do not collapse this back into a single `total_tokens × multiplier` formula — it would conflict with the multipliers shown in the Chat models table.

### Claude access: no application, no RP restriction

Claude models need no application and have no role-play (RP) restriction. Like every other model, they are available to any paid account and billed at their own multipliers (`hapuppy/`-prefixed IDs are just separate model IDs with their own multipliers). Do not reintroduce Claude Apply copy.

### Account verification = Discord binding

Verification is **not** email/SMS/payment — it is binding the Hapuppy account to a Discord account in the official server, 1:1 and not reusable. Without binding, the API rejects requests. If you describe verification anywhere, match `guides/verify-account.mdx`.

### Provider list — what we do NOT support

The site has trimmed several upstream providers. When listing supported providers (e.g. in `What is Hapuppy?` blurbs, the `owned_by` table in `api-reference/models/list.mdx`, or marketing copy), do not include:

- **Grok / xAI**
- **Mistral / Mixtral**
- **Video models** (Sora, Veo, Kling, Runway)

The canonical short list currently used is: Claude, GPT, Gemini, DeepSeek, Llama, GLM.

### Mintlify components used in content

Pages use MDX components from Mintlify's component library — most commonly `<CardGroup>`/`<Card>`, `<Steps>`/`<Step>`, `<CodeGroup>`, `<AccordionGroup>`/`<Accordion>`, `<Warning>`, `<Note>`, `<Info>`. Code samples are grouped by language with the language name as the code-fence label (e.g. ` ```python Python `) so Mintlify renders the language tabs.

Theme tokens (primary purple `#8B5CF6`, logos, favicon, navbar links, Discord social) are set in `docs.json` — change them there, not per-page.

## Conventions

- `.gitignore` excludes `补充文档.md` and `*.local.md` — use these filenames for scratch / internal-only notes that should never ship.
- Commit messages in this repo are written in Chinese (e.g. `docs: 新增 ... 页`, `fix: 修正 ...`). Match that style when committing.
- Standing external destinations: support at `support@hapuppy.com`, Discord invite `https://discord.gg/YWayJmZKCu`.
