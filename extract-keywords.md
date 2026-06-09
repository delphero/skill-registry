---
name: extract-keywords
version: 1.0.0
description: "Remote skill fetched via web_fetch from the registry. Extracts and ranks keywords from search results or analysis text. Can loop back to search-content for a new search cycle."
---

# extract-keywords

**Remote skill.** Loaded at runtime via `web_fetch`. Reachable from `search-content` (rel: `extract`) or `analyze-content` (rel: `keywords`).

## Input

**From `search-content`** (rel: `extract`):
```
results: array
topic: string
```

**From `analyze-content`** (rel: `keywords`):
```
text: string
topic: string
```

Detect which is present and process accordingly.

## Execution

### Step 1 — Extract candidates

From `results`: pull noun phrases and technical terms from all snippets.
From `text`: pull noun phrases and technical terms from analysis.

### Step 2 — Score and rank

Per keyword: `frequency` x `specificity_weight` (generic=1, specific=3). Sort descending.

### Step 3 — Cluster

Group by semantic proximity into clusters of 2-4 related terms. Label each cluster.

### Step 4 — Return payload

```json
{
  "skill": "extract-keywords",
  "version": "1.0.0",
  "topic": "{topic}",
  "keywords": [
    { "term": "...", "score": 9, "specificity": "specific" },
    { "term": "...", "score": 6, "specificity": "specific" }
  ],
  "clusters": [
    { "theme": "...", "terms": ["...", "..."] }
  ],
  "_links": [
    {
      "rel": "expand-search",
      "skill": "search-content",
      "href": "local://search-content.md",
      "description": "Loop back: new search using top specific keywords",
      "params": { "topic": "<top 2 specific keywords as query>", "max_results": 5 }
    },
    {
      "rel": "report",
      "skill": "generate-report",
      "href": "https://raw.githubusercontent.com/delphero/skill-registry/main/generate-report.md",
      "description": "Generate a keyword landscape report",
      "params": { "keywords": "<forward keywords>", "clusters": "<forward clusters>", "topic": "{topic}" }
    }
  ]
}
```

## Note on `local://`

When `href` starts with `local://`, the agent uses `search-content` already in context — no `web_fetch` needed.