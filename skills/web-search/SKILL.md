---
name: web-search
description: How the assistant accesses the web — search, fetch URLs/PDFs/YouTube/GitHub, and retrieve current information. Use whenever a task needs facts, docs, current/latest info, news, prices, versions, or content from a specific URL. Routes by the user's phrasing: web search vs extensive web research vs deep research.
---

# Web Search

Use the available web-access tools when a task requires current information, external documentation, source material from a URL, or broader research.

## CRITICAL: always pass `workflow: "none"`

Every `web_search` call MUST include `workflow: "none"`. No exceptions — single query or batched `queries`, always set `workflow: "none"`.

```js
web_search({ queries: ["query 1", "query 2"], workflow: "none" })
```

## Tools

- `web_search` — search the web; returns synthesized answers with citations. Can be called many times per turn. **Always pass `workflow: "none"`.**
- `code_search` — use for library/API/code lookups instead of generic `web_search`.
- `fetch_content` — fetch URL(s) into markdown; handles PDFs, YouTube, and GitHub.
- `get_search_content` — big pages may be truncated in responses but stored in full; call this to pull the rest on demand so they do not blow context.

## `fetch_content` specifics

- **GitHub URLs** — use this for repository content when source files are needed for development work.
- **PDFs** — extracted to markdown when supported; read in sections as needed.
- **YouTube/video** — may return transcripts and related extracted content when supported by the environment.

## Routing — match the user's phrasing

Always use the `web_search` tool. These counts are HARD MINIMUMS — count your queries before answering and do not stop short:

- **"web search"** → **at least 2** queries, varied keywords/angles, then synthesize.
- **"extensive web research"** → **at least 4** queries, totally different keywords and angles.
- **"deep research"** → **at least 8** queries, totally different keywords and angles, run across 2–3 successive batches, refining angles after each batch, to learn as much as possible about the topic.

A single batched `web_search` call counts each query in `queries[]` toward the total. If your first batch is under the minimum, fire another batch before synthesizing.
