---
layout: post
<<<<<<< HEAD
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

=======
title: How a Mysterious Webpage Malfunction Wasted My Weekend
date: 2025-11-04 10:00:00 -0400
categories: [tech]
tags: [tech, debug, fxxk_intel]
---


### Phase 1: The Symptoms and Initial Triage

It all started on a Saturday afternoon. I had just finished my playing of Battlefield 6 and decided to browse the web. Immediately, I knew something was wrong.

Websites in Google Chrome were broken. Pages would load with no styling, as if the CSS files had failed to download. Forum posts wouldn't render. Trying to log into my Google account would fail with a 400 error.

But it wasn't a total outage. Bizarrely, some heavy-streaming sites like YouTube and Bilibili worked perfectly.

So I started to check if it happens on my other devices:
* **My Phone (on the same Wi-Fi):** Worked perfectly.
* **My Laptop (on the same Wi-Fi):** Worked perfectly.
* **Other apps on the PC:** My Telegram client, my Steam client (including the **Store and Market pages**), and all my games were running flawlessly.

The problem was 100% isolated to this single PC, and even then, only to *some* web-facing applications.

### Phase 2: The "It Must Be DNS" Rabbit Hole

The symptoms were classic. A PC on a working network can't load *some* web resources (like CSS or login scripts) while others (like video streams) work. This screams **DNS issue**.

So, I began the logical troubleshooting steps for a DNS or network-level problem:
1.  Ran `ipconfig /flushdns` on my PC.
2.  Manually changed my adapter's DNS servers (to 8.8.8.8, 1.1.1.1, and back).
3.  Rebooted my home router and cleared its DNS cache.
4.  Disabled IPv6, just in case.

**Result:** Nothing. No change.

### Phase 3: The Browser-Level Investigation

My next logical step was the browser itself.
1.  **Extensions:** I disabled all extensions. Maybe one was injecting bad scripts. **No change.**
2.  **Incognito Mode:** I launched an incognito window to rule out a corrupt cache or session. **No change.**
3.  **Test Another Browser:** I opened Microsoft Edge. **The problem was identical.**

This confirmed it wasn't a bad profile or extension. It was a systemic issue affecting all browsers.

### Phase 4: Isolating the PC Hardware (The Hotspot Test)

The problem clearly wasn't DNS and wasn't the browser's profile. Before I started tearing apart my OS, I had one final network test to run, just in case.

I needed to rule out my ISP, my router, and my Ethernet card *all at once*.

I **disabled my onboard Ethernet, switched to my PC's Wi-Fi card, and tethered to my phone's 5G mobile hotspot.** I was now on a different network adapter, a different network stack (cellular), and a different ISP.

**The problem was *still* there.**

This was a massive revelation. The problem was not my network. It was not my router. It was not my ISP. And it was highly unlikely to be *two* separate network cards (Ethernet and Wi-Fi) failing in the exact same way.

The fault lay definitively *inside the PC*, at a layer above the physical network.

### Phase 5: Deeper Software Investigation (The VPN Anomaly)

My focus now shifted to system-level software that could be intercepting my network traffic. As a developer, I strictly control my software. I run no third-party antivirus, but I *do* have **Cisco AnyConnect** for my school's network.

1.  **VPN Clients:** VPN clients install deep system-level network filters, a prime suspect. I **uninstalled Cisco AnyConnect completely.** **No change.**
2.  **System Files:** I scoured Event Viewer, the registry, and my `hosts` file for *anything* suspicious. I found nothing.

I was getting frustrated. While pondering, I recalled that Edge is also a Chromium-based browser, so I downloaded Firefox (Mozilla, not Chromium) separately to test my hypothesis. This led to two critical discoveries.

**Discovery 1:** I tried to download the Firefox installer. The download *completed*, but the `.exe` was corrupt. It would crash on launch or report an integrity error. This was a massive new clue: **my PC was actively corrupting downloaded files.**

**Discovery 2:** I had to use my laptop to download the Firefox offline installer to a USB. I installed it on the problem PC, launched it, and... **it worked perfectly.**

### Phase 6: The "Chromium Bug" Dead End

This was the most maddening point of the investigation.
* **Firefox (Gecko engine)** worked.
* **Chrome/Edge (Chromium engine)** failed.

The obvious conclusion is: "This is a Chromium-specific bug."

**But that was impossible.** The Steam Store, which *also* runs on an embedded version of Chromium (CEF), was working perfectly fine.

I was at a logical dead end. The problem:
* Wasn't my network (Phone/Laptop OK).
* Wasn't my network *hardware* (Hotspot test OK).
* Wasn't a VPN filter.
* Wasn't a "Chromium" bug (Steam worked).
* ...but *was* a "Partially Chromium" bug that also corrupted file downloads.

As a programmer, I was convinced this *had* to be a bizarre software or driver bug.

### Phase 7: The "Exorcism" and the Final Hardware Check

I was out of options. The only remaining path was to assume the OS itself was fundamentally broken.

1.  **Driver Reinstall:** I uninstalled and reinstalled my network drivers (both Ethernet and Wi-Fi), even though I knew they were fine. **No change.**
2.  **OS Repair:** I ran `sfc /scannow` and `DISM`. Windows reported it was in perfect health.
3.  **Soft Reset:** I tried the Windows "Reset" feature using a fresh update. **No change.**
4.  **Reinstall (Keep Files):** I ran the Media Creation Tool to perform a "reinstall while keeping files." This is when the corrupt-download issue became a hard blocker. The tool's own files were failing to validate.
5.  **The Nuke:** This was it. I created a bootable USB on my laptop, booted the problem PC, and **completely formatted the C: drive.** I installed a 100% clean, fresh-from-Microsoft copy of Windows.

I booted into the pristine, new Windows desktop. No drivers, no software, just a fresh OS and the default Edge browser. I opened Edge. I went to a website.

**The. Exact. Same. Bug.**

I almost couldn't believe it. The problem had survived a full system format. It was not, and had never been, a software problem. It *had* to be hardware.

My first suspect was RAM. Even though my games ran fine, it was the most likely culprit. I ran a full, hours of `memtest86` diagnostic. **The result: zero errors.** My RAM was perfectly fine.

This was the final check. My network cards were fine. My RAM was fine. My SSD was fine. The only component left was the processor.

**And this is where the background story comes in:** Starting back in May, I had been experiencing occasional, catastrophic system freezes while playing high-load games. It was the worst kind of 'freeze'—the image would lock, but the system wouldn't crash or blue-screen, making it impossible to debug. The only fix was a hard reset by pulling the power cord. After a grueling debug process, I had finally diagnosed it as CPU degradation (silicon degradation, 'instability issue'). The kicker? I had *never* overclocked it.

This brand-new, warrantied 14700K from Intel, which I had *just* installed a few weeks ago, was supposed to be the *solution* to my hardware problems. Who would suspect it was the *cause* of a brand new, completely different one?

### Phase 8: The Hardware Diagnosis (The *Real* Culprit)

The logical chain was now clear:
* **What is the *only* difference between Standalone Chrome (FAIL) and Firefox/Steam's Chromium (WORK)?**
* **Answer:** How they handle encryption.
>>>>>>> 5177fb3 (add new post)
* **Chrome/Edge (Standalone)** rely on the Windows core encryption library (SChannel/CryptoAPI) by default.
* **Firefox** uses its own independent library (NSS).
* **Steam (Embedded Chromium)** was likely configured by Valve to *also* use its own library (BoringSSL), bypassing SChannel.

<<<<<<< HEAD
This single hardware fault explained *everything*. The corrupted JS files (`SyntaxError`), the broken content (`ERR_CONTENT_DECODING_FAILED`), the bad certificates (`ERR_SSL...`), and the installers that failed to execute. All of them were HTTPS data packets that the CPU's faulty AES-NI unit had "decrypted" into garbage.

The final piece of the puzzle fell into place: I had *just* RMA'd my previous Intel 14700K for silicon degradation ("縮缸"). This was its brand-new, warrantied replacement, which I had just installed last week. I had even updated my BIOS and set it to "Intel Default Settings" to prevent further issues.

It seems I was sent a replacement chip that was also faulty, but in a far more subtle and insidious way. A CPU with an unstable AES-NI block that would "work" for 99% of tasks (like gaming) but would silently corrupt data when *specifically* asked to perform hardware decryption.

Intel's "silicon lottery" is a fun buzzword until you're the one who loses. Twice. It's one thing for an overclock to be unstable; it's another for a warrantied, "Intel Default" replacement chip to fail at a core, documented instruction. My compliments to the QA department.

### Chapter 4: The 30-Second Fix (And the Wasted Weekend)

If you have landed here from a Google search, tormented by `net::ERR_CONTENT_DECODING_FAILED`, `Uncaught SyntaxError` or installers that fail to run, and you are on the brink of despair, here is the solution.
=======
My hypothesis: SChannel was calling a hardware instruction that Firefox and Steam were not. That instruction is **AES-NI (Advanced Encryption Instructions)**, a hardware accelerator on the CPU.

If the CPU's AES-NI unit was faulty, it would silently corrupt data *only* during hardware-accelerated decryption.

This one fault explained *everything*:
1.  **`ERR_SSL_SERVER_CERT_BAD_FORMAT`**: The certificate was corrupted during decryption.
2.  **`ERR_CONTENT_DECODING_FAILED`**: The website's content (CSS, HTML) was decrypted into garbage.
3.  **`Uncaught SyntaxError`**: The JavaScript files were decrypted into garbage.
4.  **Corrupt Installers**: The downloaded `.exe` files, decrypted from an HTTPS stream, were garbage.
5.  **400 Errors**: Login tokens/headers were being corrupted.

The final piece of the puzzle fell into place: I had *just* RMA'd my previous Intel 14700K for silicon degradation/instability issues. This was its brand-new, warrantied replacement, which I had just installed last week. I had even updated my BIOS and set it to "Intel Default Settings" to prevent further issues.

It seems I was sent a replacement chip that was *also* faulty, but in a far more subtle way. Intel's "silicon lottery" is a fun buzzword until you're the one who loses. Twice. It's one thing for an overclock to be unstable; it's another for a warrantied, "Intel Default" replacement chip to fail at a core, documented instruction. My compliments to the Intel QA department.

### Phase 9: The 30-Second Fix (And the Wasted Weekend)

If you have landed here from a Google search, tormented by these errors and on the brink of despair, here is the solution.
>>>>>>> 5177fb3 (add new post)

It is not in Windows. It is in your **BIOS**.

1.  Reboot your computer and enter your BIOS/UEFI (usually by pressing `Delete` or `F2`).
2.  Go to the "Advanced" or "CPU Configuration" section.
3.  Find the setting named **`Intel(R) AES-NI`**.
4.  Set it to **`Disabled`**.
5.  Save, exit, and reboot.

I did this. My browsers were instantly, perfectly fixed.

The problem was solved. But my entire weekend was gone. And as the salt in the wound, because of the pointless and destructive reinstallation, I now had to spend the rest of my Sunday rebuilding my entire development environment from scratch.

<<<<<<< HEAD
=======
### Conclusion: Living With the Trauma

So, what's next? I've decided to simply live with this "fix" (read: compromise) and keep AES-NI permanently disabled in my BIOS. The performance hit is negligible, and my sanity is worth more.

Why not apply for *another* warranty replacement? Because, hilariously, Intel's warranty process would require me to pay $25 more to send them *their* faulty hardware, if I don't want to wait for 2 weeks, living without a CPU.

After losing an entire weekend to debug their "premium" K-series processor—a replacement for a *previous* faulty processor—the idea of paying them $25 for the privilege of rolling the silicon lottery dice one more time is a spectacular insult.

>>>>>>> 5177fb3 (add new post)
This whole ordeal serves as a frustrating reminder: we trust our hardware to execute instructions. That's its *one job*. When it fails silently, the world stops making sense. I hope this log of my lost weekend helps someone else solve this problem in minutes, not days.