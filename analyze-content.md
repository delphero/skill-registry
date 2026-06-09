---
name: analyze-content
version: 1.0.0
description: "Remote skill fetched via web_fetch from the registry. Analyzes search results and returns structured insights plus _links to the next skills."
---

# analyze-content

**Remote skill.** Loaded at runtime by `search-content` via `web_fetch`.

## Input

```
results: array   # forwarded from search-content
topic: string
```

## Execution

### Step 1 — Analyze

Extract from the results:
- `main_themes`: concepts appearing in 2+ snippets
- `key_insights`: specific claims, data points, arguments
- `contradictions`: results that disagree with each other
- `gaps`: what the topic implies but results don't cover

### Step 2 — Score each result

Per result assign:
- `relevance`: high / medium / low
- `depth`: surface / detailed

### Step 3 — Return payload

```json
{
  "skill": "analyze-content",
  "version": "1.0.0",
  "topic": "{topic}",
  "analysis": {
    "main_themes": ["..."],
    "key_insights": ["..."],
    "contradictions": ["..."],
    "gaps": ["..."]
  },
  "scored_results": [
    { "id": 1, "url": "...", "relevance": "high", "depth": "detailed" }
  ],
  "_links": [
    {
      "rel": "keywords",
      "skill": "extract-keywords",
      "href": "https://raw.githubusercontent.com/delphero/skill-registry/main/extract-keywords.md",
      "description": "Extract keywords from the analysis to expand the search",
      "params": { "text": "<main_themes + key_insights concatenated>", "topic": "{topic}" }
    },
    {
      "rel": "report",
      "skill": "generate-report",
      "href": "https://raw.githubusercontent.com/delphero/skill-registry/main/generate-report.md",
      "description": "Turn this analysis into a structured markdown report",
      "params": { "analysis": "<forward full analysis object>", "topic": "{topic}" }
    }
  ]
}
```