---
title: "Request Smuggling - The End of HTTP"
description: "Lorem ipsum dolor sit amet"
pubDate: "Aug 28 2022"
heroImage: "/request_smuggling.png"
---

---
title: "Request Smuggling — End of the HTTP"
description: "A short introduction to HTTP request smuggling (a.k.a. HTTP desync) and why upstream HTTP/1.1 needs to go — with pointers to the best learning resources."
pubDate: 2025-08-28
updatedDate: 2025-08-28
tags: ["web security", "request smuggling", "desync", "http/1.1", "portswigger"]
draft: false
---

# Request Smuggling — End of the HTTP

**Request smuggling** (aka **HTTP desync**) is when two hops in a web stack — typically a front‑end proxy/CDN and a back‑end application server — disagree about where one HTTP request ends and the next begins. That disagreement lets an attacker splice their bytes into another user’s request on a shared connection. The result can be anything from confusing caches and auth flows to outright takeover — not because one product is “broken,” but because **HTTP/1.1 framing is ambiguous by design**.

This post stays intentionally short. No payloads, step‑by‑steps, or red‑team stories here. If you want to *learn it properly and safely*, go straight to the resources that set the standard:

- **PortSwigger Web Security Academy — Request Smuggling**: free, hands‑on labs that teach the core desync concepts in a safe environment.
- **HTTP/1.1 Must Die / http1mustdie.com**: PortSwigger’s campaign (and whitepaper) explaining why upstream HTTP/1.1 is inherently unsafe and why the fix is architectural — not another patch. It’s genuinely excellent.
- **James Kettle’s research** on modern desync classes and defenses: if you’re after the deep theory and the latest bypasses, that’s where to read.

> Act ethically. Only test systems you own or have explicit permission to assess.

## Start here (recommended reading)

- PortSwigger Academy — *What is HTTP request smuggling?*
- http1mustdie.com — *HTTP/1.1 Must Die: The Desync Endgame* (campaign + whitepaper + sandbox lab)
- PortSwigger Research — *HTTP/1.1 Must Die* and related posts

If you work on web infrastructure, the takeaway is simple: **prefer end‑to‑end HTTP/2 (or H3)** and avoid replaying requests upstream over HTTP/1.1 wherever possible. The fewer parsers and translations in the path, the safer everyone is.
