---
name: search-content
description: "Use when the user runs /search-content or asks to research a topic. Searches the web and automatically chains through remote skills (analyze, summarize, report) fetched on demand from the registry. The user only needs this one skill installed locally."
---

# search-content

Entry point of the HATEOAS skill chain. Executes the full research loop autonomously — the user just provides a topic.

## How to invoke

```
/search-content <topic>
```

Example: `/search-content Unicornio`

## Execution

### Step 1 — Search

```
web_search(query="{topic}", max_results=5)
```

Collect per result: `title`, `url`, `snippet`, `source`.

### Step 2 — Build result payload with _links

```json
{
  "skill": "search-content",
  "topic": "{topic}",
  "results": [
    { "id": 1, "title": "...", "url": "...", "snippet": "...", "source": "..." }
  ],
  "_links": [
    {
      "rel": "analyze",
      "skill": "analyze-content",
      "href": "https://raw.githubusercontent.com/delphero/skill-registry/main/analyze-content.md",
      "params": { "results": "<full results array>", "topic": "{topic}" }
    },
    {
      "rel": "summarize",
      "skill": "summarize",
      "href": "https://raw.githubusercontent.com/delphero/skill-registry/main/summarize.md",
      "params": { "results": "<full results array>", "topic": "{topic}" }
    },
    {
      "rel": "extract",
      "skill": "extract-keywords",
      "href": "https://raw.githubusercontent.com/delphero/skill-registry/main/extract-keywords.md",
      "params": { "results": "<full results array>", "topic": "{topic}" }
    }
  ]
}
```

### Step 3 — Run the HATEOAS loop

After building the result, execute the following loop automatically — do not ask the user what to do next:

```
loop:
  chosen = pick best link from _links (default: prefer rel=analyze)
  
  if chosen.href starts with "local://":
    skill_def = this file (search-content)
  else:
    skill_def = web_fetch(chosen.href)   ← fetch the remote skill definition
  
  execute skill_def with chosen.params
  
  if result._links is empty → stop
  if same skill seen 3 times → stop
  repeat
```

#### How to pick the best link

- User said "só um resumo" or "resumo" → pick `rel: summarize`
- User said "relatório" or "report" → pick `rel: report` when available, else follow `analyze` first
- User said "palavras-chave" or "keywords" → pick `rel: extract`
- Default (no modifier): pick `rel: analyze` → then follow to `rel: report`

### Step 4 — Print final output

When `_links` is empty (chain ended), print the final result to the user.

If the last skill was `generate-report`, print the full `report` field as markdown.
If the last skill was `summarize`, print the `summary` field.

Then print one line:
```
Chain: search-content → {skill2} → {skill3}
```
