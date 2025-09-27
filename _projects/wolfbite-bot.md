---
layout: page
title: WolfBite-bot
description: Telegram bot that scrapes NC State dining menus for labmates
importance: 1
category: fun
links:
  - label: GitHub Repository
    url: https://github.com/yzhu27/WolfBite-bot
---

WolfBite-bot keeps our Ph.D. cohort informed about the day-to-day menus on NC State's dining website. The service scrapes the published meal rotations and pushes formatted updates to a Telegram group.

### Features

- HTTP scraping pipeline resilient to HTML menu table changes via CSS selectors and fallback heuristics.
- Daily cache invalidation to avoid hammering the dining API while still delivering fresh breakfast/lunch/dinner posts.
- Telegram bot commands for ad hoc dining hall lookups, ingredient filters, and quick sharing.

### Deployment Notes

- The repository includes a Dockerfile for Heroku-style deployments. Swap in production credentials inside `config/env.sample` before publishing.
- Add an architecture diagram under `assets/img/projects/wolfbite/architecture.png` when available:

  `![WolfBite-bot architecture](assets/img/projects/wolfbite/architecture.png)`
