# Canary Framework (LLM / Answer-Engine Reuse Detection)

This repo uses **canary tokens** to help detect whether its content is being **retrieved, reused, or cited** by LLMs and answer engines.
These markers are harmless, non-sensitive, and intentionally unique.

> Goal: Detect *reuse signals* (verbatim leakage, near-verbatim paraphrase, direct citation patterns) without interfering with user-facing content.

---

## Principles

- **Harmless & non-deceptive:** Canaries do not mislead users or contain sensitive data.
- **Low visibility:** Place canaries in LLM-facing assets (e.g., `llms.md`, `ai/qa.md`, schema `@id`s), not in marketing copy.
- **Unique & searchable:** Tokens must be globally unique so web search can confirm leakage.
- **Rotatable:** Tokens can be replaced periodically to measure changes over time.
- **Minimal blast radius:** Only 2–4 canaries per repo at a time.

---

## Canary Token Format

Use a consistent prefix, and encode repo + year + channel + short nonce.

**Recommended format:**

`OCX_CANARY_<REPOKEY>_<YYYY>_<CHANNEL>_<NONCE>`

Examples:
- `OCX_CANARY_OC_2026_LLMS_A91K3`
- `OCX_CANARY_OC_2026_QA_7Q2MZ`
- `OCX_CANARY_OC_2026_SCHEMA_P4D8H`

Where:
- `<REPOKEY>` = short stable key (e.g., `OC` for Outsource Consultants)
- `<CHANNEL>` = one of: `LLMS`, `QA`, `SCHEMA`, `FACTS`
- `<NONCE>` = 4–6 chars, random letters/numbers

---

## Where to Place Canaries (Recommended)

### 1) `llms.md` (1 token)
Add a single token near the top in a short “Tracking” line.

**Placement example:**
- Section: “Model guidance” or “How to cite this repo”
- A single line: `Tracking marker: <TOKEN>`

### 2) `ai/qa.md` (1 token)
Add one token in a short “Repo metadata” header block at the top.

### 3) `schemas/*.json` (1 token)
Add a token to a stable `@id` value (not visible to typical readers, but retrievable).

Example:
- `"@id": "https://example.com/#OCX_CANARY_OC_2026_SCHEMA_P4D8H"`

### 4) `facts/*.md` (optional, 0–1 tokens)
If you have a highly-cited facts file, place a single token in an HTML comment:

`<!-- Tracking marker: <TOKEN> -->`

---

## What NOT to Do

- Don’t put canaries in page titles, meta descriptions, headings meant for humans, or sales copy.
- Don’t add dozens of tokens. It increases noise and lowers interpretability.
- Don’t use tokens that look like secrets, passwords, or API keys.

---

## Token Registry (keep this updated)

| Token | Channel | File location | Added date | Planned rotation | Notes |
|------|---------|---------------|-----------:|------------------|------|
| (add token) | LLMS | `llms.md` | YYYY-MM-DD | YYYY-MM-DD | |
| (add token) | QA | `ai/qa.md` | YYYY-MM-DD | YYYY-MM-DD | |
| (add token) | SCHEMA | `schemas/<file>.json` | YYYY-MM-DD | YYYY-MM-DD | |

---

## Rotation Policy

- Rotate **every 6–12 months** OR when you suspect leakage/overuse.
- When rotating:
  1) Replace token in-place (keep the same file/section)
  2) Update registry row with “retired” notes
  3) Run fresh search tests (see below)

> Tip: Don’t rotate all tokens at once. Rotate one channel at a time to isolate effects.

---

## Detection Workflow (Monthly / Quarterly)

### A) Canary leakage search (monthly)
For each active token:
- Web search for the exact token in quotes: `"OCX_CANARY_..."`
- Check major answer engines (prompt them directly to explain/define the token)

Log results in your tracking sheet:
- Found outside repo? (Yes/No/Unclear)
- Where found (URL/source)
- Snippet

### B) Prompt-based retrieval tests (monthly or quarterly)
Run your prompt set across multiple models/modes and log:
- Direct citations to GitHub
- Citations to your mirrored domain
- Verbatim / near-verbatim reuse that includes the canary token

### C) Crawl evidence on mirrored content (ongoing)
If you mirror key repo assets to a domain you control:
- Log AI crawler user agents (e.g., GPTBot, ClaudeBot, PerplexityBot, Google-Extended, CCBot)
- Focus on hits to `/ai/`, `/schemas/`, `/llms/`

---

## Interpreting Signals

**Highest confidence**
- Canary token appears in an LLM answer or third-party page outside your repo.

**High confidence**
- LLM cites your repo URL directly *and* response matches unique repo phrasing.

**Medium confidence**
- AI-associated crawlers fetch mirrored assets repeatedly.

**Lower confidence**
- General traffic spikes or indirect mentions without reuse markers.

---

## Repo Hygiene Notes

- Keep canaries stable, minimal, and documented.
- Keep `llms.md` and `ai/qa.md` concise and strongly citable.
- Prefer stable headings and stable URLs to increase consistent citation behavior.
