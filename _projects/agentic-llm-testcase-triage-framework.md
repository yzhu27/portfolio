---
layout: page
title: Agentic LLM Testcase Triage Framework
description: Automating IVI/ADAS failure triage with retrieval-augmented agents and vector search
importance: 0
category: work
links:
  - label: Qualcomm Internship Recap
    url: /news/2025-06-16-qualcomm
---

During my 2025 internship with Qualcomm's Automotive IVI/ADAS team, I designed an agentic retrieval-augmented generation (RAG) workflow that triages more than ten thousand integration tests per day.

### Highlights

- Consolidated multi-source IVI/ADAS logs into concise error narratives using large-language-model summarization seeded with error-anchor context windows.
- Indexed structured traces inside ChromaDB/FAISS to enable hybrid semantic + keyword retrieval that surfaces the most relevant comparator incidents.
- Coordinated LangChain agents, function-calling tools, and JSON-schema emitters so downstream dashboards receive machine-actionable diagnostics within minutes of test completion.

