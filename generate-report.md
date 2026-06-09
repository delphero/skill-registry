---
name: generate-report
version: 1.0.0
description: "Remote skill fetched via web_fetch from the registry. Terminal skill — generates a structured markdown report and returns empty _links to signal end of chain."
---

# generate-report

**Remote skill.** Loaded at runtime via `web_fetch`. Terminal skill — ends the chain.

## Input

Accepts any combination of:
```
topic: string
analysis?: object    # from analyze-content
keywords?: array     # from extract-keywords
clusters?: array     # from extract-keywords
summary?: string     # from summarize
```

## Execution

Produce a structured markdown report with:

1. **Title**: Research report on {topic}
2. **Executive summary**: 2-3 sentences
3. **Main themes**: from analysis or keyword clusters
4. **Key insights**: bullet list
5. **Keywords**: table with term, score, cluster
6. **Sources**: list with title and url
7. **Gaps**: what was not found

### Return payload

```json
{
  "skill": "generate-report",
  "version": "1.0.0",
  "topic": "{topic}",
  "report": "<full markdown report>",
  "_links": []
}
```

`_links` is empty — the agent stops the chain here and prints the `report` field to the user.