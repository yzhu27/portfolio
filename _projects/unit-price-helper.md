---
layout: page
title: Unit Price Helper
description: Chrome extension that overlays unit pricing on major grocery retailers
importance: 2
category: fun
links:
  - label: GitHub Repository
    url: https://github.com/yzhu27/UnitPriceHelper
---

Online grocery stores rarely expose unit prices in a consistent way. Unit Price Helper tackles that pain point by injecting price-per-unit badges across Harris Teeter, Costco, Target, and more.

### What It Does

- Parses product grids and detail pages, normalizing quantities (fl oz, count, lb) into a comparable base unit.
- Renders inline badges using lightweight DOM patches so shoppers can immediately see which option is more economical.
- Auto-updates retailer-specific scraping logic through a small ruleset that ships as part of the extension package.

### Try It Out

- Clone the repo, run `npm install`, and load the `dist/` folder into Chrome's developer mode.
- Consider adding a screencast at `assets/img/projects/unit-price-helper/demo.gif` to showcase side-by-side price comparisons:

  `![Unit Price Helper demo](assets/img/projects/unit-price-helper/demo.gif)`
