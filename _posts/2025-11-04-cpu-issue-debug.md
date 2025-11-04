---
layout: post
title: A Weekend Lost to a Strange Bug
date: 2025-11-04 10:00:00 -0400
categories: [tech]
tags: [tech, debug, life]
---

### The Problem Begins

It all started on an otherwise unremarkable Saturday afternoon. I was browsing the web on my PC, and suddenly, things started to break.

It wasn't a total outage. Instead, it was something more insidious. Websites in Google Chrome would fail to load correctly, displaying broken layouts, missing images, or failing to function. Investigating the developer console revealed a torrent of errors.

Alongside the primary network errors like `net::ERR_CONTENT_DECODING_FAILED` and `net::ERR_SSL_SERVER_CERT_BAD_FORMAT`, I saw dozens of JavaScript `Uncaught SyntaxError` violations:
`missing ":"`
`Unexpected token '}'`
`unclosed "{"`

This wasn't a single script failing; it was as if the JS files themselves were being fed to the browser as gibberish.

### Chapter 1: The Impossible Contradiction

Like any tech-savvy user, I started with the most logical, least invasive steps.

**Hypothesis 1: A rogue extension or bad profile.**
I disabled all extensions in Chrome. No change.
I then tested in Microsoft Edge. The problem persisted identically. This was a crucial clue: it wasn't my Chrome profile, but something systemic affecting all Chromium-based browsers.

**Hypothesis 2: A DNS or Network Issue.**
The symptoms—missing resources, garbled JS—screamed "network problem." It felt like some assets weren't being resolved or retrieved correctly.

So, I went down the network rabbit hole. Hard.
* I ran `ipconfig /flushdns` on my PC.
* I manually changed my adapter's DNS servers (to 8.8.8.8, 1.1.1.1, and back).
* I rebooted my home router and cleared its DNS cache.
* I disabled IPv6.
* I ran `netsh winsock reset` and every other network-reset command I could think of.

Nothing worked.

**Hypothesis 3: The Great Contradiction**
This is where things stopped making sense. I launched other applications to test my connection.
* My Telegram desktop client? **Worked perfectly.**
* My games (like *Battlefield 6*)? **Worked perfectly.**
* I downloaded the Firefox offline installer (using another PC, as my own machine couldn't even download installers reliably) and installed it. **Firefox worked perfectly.**

So, this wasn't a network problem. It was a "Chromium problem." ...Right?

**Wrong.**

I opened my Steam client. The chat, the library, all worked. Then I opened the **Steam Store and Market pages**... and they **worked perfectly.**

This was the maddening clue. The Steam Store *is* a Chromium-based browser (CEF).

So, the problem was:
1.  **Not** a network issue (Firefox/Telegram/Games were fine).
2.  **Not** a "Chromium" issue (Steam's embedded Chromium was fine).
3.  **Only** a "Standalone Chrome/Edge" issue.
4.  It also caused **file downloads** to become corrupted, failing integrity checks or silently crashing when run.

### Chapter 2: The Drastic Measure and the Utter Despair

By now, my entire Saturday evening was consumed. My only logical conclusion was that my Windows installation was so deeply and specifically corrupted that only standalone browsers were affected, while Steam's implementation somehow wasn't.

As Saturday night turned into Sunday morning, I was out of patience. I triggered the "nuclear option."

**I did a full, clean reinstallation of Windows.**

This wasn't a "Reset this PC" with files kept. This was booting from a fresh USB installer, **deleting every partition** on my system drive, and formatting it clean.

Half of my Sunday was spent on this process, followed by the tedious reinstallation of drivers and basic applications. With a sense of weary certainty, I downloaded Chrome.

**And the bug was still there.**

I cannot overstate the feeling of existential dread at this moment. The problem had survived a full OS format. This is where I confirmed, without a shadow of a doubt, that the problem was *inside the box*.

To be 100% certain, I disabled my onboard Ethernet port, switched to a separate Wi-Fi card, and connected my PC to my **phone's mobile hotspot**. I was now on a different network adapter, a different network stack (cellular), and a different ISP.

**The problem was *still* there.**

It wasn't the software (the OS was new). It wasn't the drivers (they were fresh). It wasn't my router, my modem, my ISP, or even my Ethernet port.

It couldn't be my RAM (my *Battlefield 6* test proved that; a high-load game would be far more sensitive to bad RAM).

The problem was local to the PC, independent of the OS, and independent of all network hardware. What was left?

### Chapter 3: The Unthinkable Culprit

The only clue that mattered remained: **Standalone Chrome/Edge (Fail) vs. Firefox/Steam's Chromium (Work).**

The difference isn't just the rendering engine. The core difference is *how they handle encryption.*

* **Chrome/Edge (Standalone)** rely on the Windows core encryption library (SChannel/CryptoAPI) by default.
* **Firefox** uses its own independent library (NSS).
* **Steam (Embedded Chromium)** was likely configured by Valve to *also* use its own library (BoringSSL), bypassing SChannel.

This single hardware fault explained *everything*. The corrupted JS files (`SyntaxError`), the broken content (`ERR_CONTENT_DECODING_FAILED`), the bad certificates (`ERR_SSL...`), and the installers that failed to execute. All of them were HTTPS data packets that the CPU's faulty AES-NI unit had "decrypted" into garbage.

The final piece of the puzzle fell into place: I had *just* RMA'd my previous Intel 14700K for silicon degradation ("縮缸"). This was its brand-new, warrantied replacement, which I had just installed last week. I had even updated my BIOS and set it to "Intel Default Settings" to prevent further issues.

It seems I was sent a replacement chip that was also faulty, but in a far more subtle and insidious way. A CPU with an unstable AES-NI block that would "work" for 99% of tasks (like gaming) but would silently corrupt data when *specifically* asked to perform hardware decryption.

Intel's "silicon lottery" is a fun buzzword until you're the one who loses. Twice. It's one thing for an overclock to be unstable; it's another for a warrantied, "Intel Default" replacement chip to fail at a core, documented instruction. My compliments to the QA department.

### Chapter 4: The 30-Second Fix (And the Wasted Weekend)

If you have landed here from a Google search, tormented by `net::ERR_CONTENT_DECODING_FAILED`, `Uncaught SyntaxError` or installers that fail to run, and you are on the brink of despair, here is the solution.

It is not in Windows. It is in your **BIOS**.

1.  Reboot your computer and enter your BIOS/UEFI (usually by pressing `Delete` or `F2`).
2.  Go to the "Advanced" or "CPU Configuration" section.
3.  Find the setting named **`Intel(R) AES-NI`**.
4.  Set it to **`Disabled`**.
5.  Save, exit, and reboot.

I did this. My browsers were instantly, perfectly fixed.

The problem was solved. But my entire weekend was gone. And as the salt in the wound, because of the pointless and destructive reinstallation, I now had to spend the rest of my Sunday rebuilding my entire development environment from scratch.

This whole ordeal serves as a frustrating reminder: we trust our hardware to execute instructions. That's its *one job*. When it fails silently, the world stops making sense. I hope this log of my lost weekend helps someone else solve this problem in minutes, not days.