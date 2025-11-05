---
layout: post
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

- **My Phone (on the same Wi-Fi):** Worked perfectly.
- **My Laptop (on the same Wi-Fi):** Worked perfectly.
- **Other apps on the PC:** My Telegram client, my Steam client (including the **Store and Market pages**), and all my games were running flawlessly.

The problem was 100% isolated to this single PC, and even then, only to _some_ web-facing applications.

### Phase 2: The "It Must Be DNS" Rabbit Hole

The symptoms were classic. A PC on a working network can't load _some_ web resources (like CSS or login scripts) while others (like video streams) work. This screams **DNS issue**.

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

I needed to rule out my ISP, my router, and my Ethernet card _all at once_.

I **disabled my onboard Ethernet, switched to my PC's Wi-Fi card, and tethered to my phone's 5G mobile hotspot.** I was now on a different network adapter, a different network stack (cellular), and a different ISP.

**The problem was _still_ there.**

This was a massive revelation. The problem was not my network. It was not my router. It was not my ISP. And it was highly unlikely to be _two_ separate network cards (Ethernet and Wi-Fi) failing in the exact same way.

The fault lay definitively _inside the PC_, at a layer above the physical network.

### Phase 5: Deeper Software Investigation (The VPN Anomaly)

My focus now shifted to system-level software that could be intercepting my network traffic. As a developer, I strictly control my software. I run no third-party antivirus, but I _do_ have **Cisco AnyConnect** for my school's network.

1.  **VPN Clients:** VPN clients install deep system-level network filters, a prime suspect. I **uninstalled Cisco AnyConnect completely.** **No change.**
2.  **System Files:** I scoured Event Viewer, the registry, and my `hosts` file for _anything_ suspicious. I found nothing.

I was getting frustrated. While pondering, I recalled that Edge is also a Chromium-based browser, so I downloaded Firefox (Mozilla, not Chromium) separately to test my hypothesis. This led to two critical discoveries.

**Discovery 1:** I tried to download the Firefox installer. The download _completed_, but the `.exe` was corrupt. It would crash on launch or report an integrity error. This was a massive new clue: **my PC was actively corrupting downloaded files.**

**Discovery 2:** I had to use my laptop to download the Firefox offline installer to a USB. I installed it on the problem PC, launched it, and... **it worked perfectly.**

### Phase 6: The "Chromium Bug" Dead End

This was the most maddening point of the investigation.

- **Firefox (Gecko engine)** worked.
- **Chrome/Edge (Chromium engine)** failed.

The obvious conclusion is: "This is a Chromium-specific bug."

**But that was impossible.** The Steam Store, which _also_ runs on an embedded version of Chromium (CEF), was working perfectly fine.

I was at a logical dead end. The problem:

- Wasn't my network (Phone/Laptop OK).
- Wasn't my network _hardware_ (Hotspot test OK).
- Wasn't a VPN filter.
- Wasn't a "Chromium" bug (Steam worked).
- ...but _was_ a "Partially Chromium" bug that also corrupted file downloads.

As a programmer, I was convinced this _had_ to be a bizarre software or driver bug.

### Phase 7: The "Exorcism" and the Final Hardware Check

I was out of options. The only remaining path was to assume the OS itself was fundamentally broken.

1.  **Driver Reinstall:** I uninstalled and reinstalled my network drivers (both Ethernet and Wi-Fi), even though I knew they were fine. **No change.**
2.  **OS Repair:** I ran `sfc /scannow` and `DISM`. Windows reported it was in perfect health.
3.  **Soft Reset:** I tried the Windows "Reset" feature using a fresh update. **No change.**
4.  **Reinstall (Keep Files):** I ran the Media Creation Tool to perform a "reinstall while keeping files." This is when the corrupt-download issue became a hard blocker. The tool's own files were failing to validate.
5.  **The Nuke:** This was it. I created a bootable USB on my laptop, booted the problem PC, and **completely formatted the C: drive.** I installed a 100% clean, fresh-from-Microsoft copy of Windows.

I booted into the pristine, new Windows desktop. No drivers, no software, just a fresh OS and the default Edge browser. I opened Edge. I went to a website.

**The. Exact. Same. Bug.**

I almost couldn't believe it. The problem had survived a full system format. It was not, and had never been, a software problem. It _had_ to be hardware.

My first suspect was RAM. Even though my games ran fine, it was the most likely culprit. I ran a full, hours of `memtest86` diagnostic. **The result: zero errors.** My RAM was perfectly fine.

This was the final check. My network cards were fine. My RAM was fine. My SSD was fine. The only component left was the processor.

**And this is where the background story comes in:** Starting back in May, I had been experiencing occasional, catastrophic system freezes while playing high-load games. It was the worst kind of 'freeze'—the image would lock, but the system wouldn't crash or blue-screen, making it impossible to debug. The only fix was a hard reset by pulling the power cord. After a grueling debug process, I had finally diagnosed it as CPU degradation (silicon degradation, 'instability issue'). The kicker? I had _never_ overclocked it.

This brand-new, warrantied 14700K from Intel, which I had _just_ installed a few weeks ago, was supposed to be the _solution_ to my hardware problems. Who would suspect it was the _cause_ of a brand new, completely different one?

### Phase 8: The Hardware Diagnosis (The _Real_ Culprit)

The logical chain was now clear:

- **What is the _only_ difference between Standalone Chrome (FAIL) and Firefox/Steam's Chromium (WORK)?**
- **Answer:** How they handle encryption.
- **Chrome/Edge (Standalone)** rely on the Windows core encryption library (SChannel/CryptoAPI) by default.
- **Firefox** uses its own independent library (NSS).
- **Steam (Embedded Chromium)** was likely configured by Valve to _also_ use its own library (BoringSSL), bypassing SChannel.

My hypothesis: SChannel was calling a hardware instruction that Firefox and Steam were not. That instruction is **AES-NI (Advanced Encryption Instructions)**, a hardware accelerator on the CPU.

If the CPU's AES-NI unit was faulty, it would silently corrupt data _only_ during hardware-accelerated decryption.

This one fault explained _everything_:

1.  **`ERR_SSL_SERVER_CERT_BAD_FORMAT`**: The certificate was corrupted during decryption.
2.  **`ERR_CONTENT_DECODING_FAILED`**: The website's content (CSS, HTML) was decrypted into garbage.
3.  **`Uncaught SyntaxError`**: The JavaScript files were decrypted into garbage.
4.  **Corrupt Installers**: The downloaded `.exe` files, decrypted from an HTTPS stream, were garbage.
5.  **400 Errors**: Login tokens/headers were being corrupted.

The final piece of the puzzle fell into place: I had _just_ RMA'd my previous Intel 14700K for silicon degradation/instability issues. This was its brand-new, warrantied replacement, which I had just installed last week. I had even updated my BIOS and set it to "Intel Default Settings" to prevent further issues.

It seems I was sent a replacement chip that was _also_ faulty, but in a far more subtle way. Intel's "silicon lottery" is a fun buzzword until you're the one who loses. Twice. It's one thing for an overclock to be unstable; it's another for a warrantied, "Intel Default" replacement chip to fail at a core, documented instruction. My compliments to the Intel QA department.

### Phase 9: The 30-Second Fix (And the Wasted Weekend)

If you have landed here from a Google search, tormented by these errors and on the brink of despair, here is the solution.

It is not in Windows. It is in your **BIOS**.

1.  Reboot your computer and enter your BIOS/UEFI (usually by pressing `Delete` or `F2`).
2.  Go to the "Advanced" or "CPU Configuration" section.
3.  Find the setting named **`Intel(R) AES-NI`** or something similar. My motherboard is a MSI, so the name may differ on your BIOS.
4.  Set it to **`Disabled`**.
5.  Save, exit, and reboot.

I did this. My browsers were instantly, perfectly fixed.

The problem was solved. But my entire weekend was gone. And as the salt in the wound, because of the pointless and destructive reinstallation, I now had to spend the rest of my Sunday rebuilding my entire development environment from scratch.

### Conclusion: Living With the Trauma

So, what's next? I've decided to simply live with this "fix" (read: compromise) and keep AES-NI permanently disabled in my BIOS. The performance hit is negligible, and my sanity is worth more.

Why not apply for _another_ warranty replacement? Because, hilariously, Intel's warranty process would require me to pay $25 more to send them _their_ faulty hardware, if I don't want to wait for 2 weeks, living without a CPU.

After losing an entire weekend to debug their "premium" K-series processor—a replacement for a _previous_ faulty processor—the idea of paying them $25 for the privilege of rolling the silicon lottery dice one more time is a spectacular insult.

I hope this log of my lost weekend helps someone else solve this problem in minutes, not days.
