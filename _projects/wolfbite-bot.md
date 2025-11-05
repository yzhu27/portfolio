---
layout: page
title: WolfBite-bot
description: Telegram bot that scrapes NC State dining menus for my PhD colleagues
importance: 1
category: fun
links:
  - label: GitHub Repository
    url: https://github.com/yzhu27/WolfBite-bot
---

#### [WolfBite Bot](https://github.com/yzhu27/WolfBite-bot)

Most days, campus dining is a gamble. To avoid walking, taking a bus, then walking, all the way to the fountain only to discover that “Chef’s Surprise” is… surprising. Thus I built WolfBite, a Telegram bot that tells you what’s actually being served before you head to the hall.

In a group chat, you simply @ the bot, pick a dining hall and meal period, and it fetches the day’s menu straight from NC State Dining’s website, parses the results, and replies with a clean, readable list. Since the halls occasionally invent dishes whose names look like Scrabble dumps, I added an optional OpenAI API-powered translation step to render those items into other languages (with a emoji!),super handy for non-native speakers.

Under the hood, it’s a tidy Python project with clear modules for the Telegram interaction, scraping/formatting, and language helpers, plus a Dockerfile for one-command deployment. It’s built for our Telegram chats: zero UI, instant answers, and far fewer wasted walks. Bon appétit.

Click here to try it -> [WolfBite Bot](https://t.me/NCSU_WolfBite_Bot)

<figure class="post-photo" style="max-width: 80%; margin: 1.5rem auto; text-align: center;">
  <img src="{{ '/assets/img/projects/wolfbite.png' | relative_url }}" alt="WolfBiteBot_features" style="width: 100%; height: auto;">
</figure>
