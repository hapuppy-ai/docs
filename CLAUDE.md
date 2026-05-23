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
- **API Reference** — everything under `api-reference/<category>/<endpoint>.mdx`

Page paths in `docs.json` are written without the `.mdx` extension and without a leading slash (e.g. `"guides/authentication"`, `"api-reference/chat/completions"`).

### Two API base URLs are used intentionally — keep them straight

This is the most common footgun when editing API examples:

- **`https://beta.hapuppy.com/v1`** — used by `api-reference/chat/completions.mdx`, `index.mdx`, `quickstart.mdx`, and `guides/faq.mdx`
- **`https://api.hapuppy.com/v1`** — used by every other endpoint page (embeddings, images, audio, video, moderations, models)

When adding new examples, match the convention of the surrounding page rather than picking one. The most recent merge (`update-api-baseurl`, PR #2) updated these deliberately — do not "normalize" them without checking with the user.

### Pricing model: one-time credit packs, not subscriptions

A prior commit (`5b80786 fix: 修正定价描述与官网一次性购买模式对齐`) specifically corrected pricing copy to match the website. Whenever you touch pricing language:

- Plans are **one-time purchases**, not monthly subscriptions. Lite $9.99 / Standard $19.99 / Pro $36.99.
- Purchased credits live in the **vault** and **never expire**.
- The Free tier (100K/day) **resets daily** and does **not** roll over.
- Rate limit across all tiers: **100 RPM**.
- Daily reset (free tier) is **midnight Beijing time (UTC 16:00)**.
- Payments are processed via **Creem**.

The root `faq.mdx` at the repo top level is **stale and orphaned** (not in `docs.json`); the live FAQ is `guides/faq.mdx`. Edits to FAQ content go in `guides/faq.mdx`.

### Mintlify components used in content

Pages use MDX components from Mintlify's component library — most commonly `<CardGroup>`/`<Card>`, `<Steps>`/`<Step>`, `<CodeGroup>`, `<AccordionGroup>`/`<Accordion>`, `<Warning>`, `<Note>`. Code samples are grouped by language with the language name as the code-fence label (e.g. ` ```python Python `) so Mintlify renders the language tabs.

Theme tokens (primary purple `#8B5CF6`, logos, favicon, navbar links, Discord social) are set in `docs.json` — change them there, not per-page.

## Conventions

- `.gitignore` excludes `补充文档.md` and `*.local.md` — use these filenames for scratch / internal-only notes that should never ship.
- Commit messages in this repo are written in Chinese (e.g. `docs: 新增 ... 页`, `fix: 修正 ...`). Match that style when committing.
- External destinations referenced from docs: dashboard at `hapuppy.com/dashboard`, models catalog at `hapuppy.com/models`, support at `support@hapuppy.com`, Discord at `https://discord.gg/YWayJmZKCu`.
