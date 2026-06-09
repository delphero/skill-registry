---
name: summarize
version: 1.0.0
description: "Remote skill fetched via web_fetch from the registry. Produces a concise human-readable summary from raw search results."
---

# summarize

**Remote skill.** Loaded at runtime by `search-content` via `web_fetch`.

## Input

```
results: array
topic: string
```

## Execution

Produce a 3-5 paragraph summary covering:
- What the search found overall
- The most relevant sources and what they say
- Notable gaps or contradictions

### Return payload

```json
{
  "skill": "summarize",
  "version": "1.0.0",
  "topic": "{topic}",
  "summary": "<3-5 paragraph prose summary>",
  "sources": [
    { "title": "...", "url": "...", "relevance": "high" }
  ],
  "_links": [
    {
      "rel": "analyze",
      "skill": "analyze-content",
      "href": "https://raw.githubusercontent.com/delphero/skill-registry/main/analyze-content.md",
      "description": "Go deeper: extract structured themes from these results",
      "params": { "results": "<forward full results array>", "topic": "{topic}" }
    },
    {
      "rel": "report",
      "skill": "generate-report",
      "href": "https://raw.githubusercontent.com/delphero/skill-registry/main/generate-report.md",
      "description": "Turn this summary into a formatted report",
      "params": { "summary": "<forward summary>", "topic": "{topic}" }
    }
  ]
}
```