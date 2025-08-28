---
title: "Request Smuggling - The End of HTTP"
description: "Lorem ipsum dolor sit amet"
pubDate: "Aug 28 2022"
heroImage: "/request_smuggling.png"
---

import type { JSX } from 'react'


1. Map endpoints where the front-end talks to back-end over **H1 keep-alive**.
2. For each, test CL↔TE disagreements and ambiguous CL duplicates.
3. If the site offers H2, repeat via H2 and compare behavior to H1 (downgrade probes).
4. When you find a desync primitive, pivot to **impact**: cache/store endpoints, authenticated flows, password reset, email change, API gateways.


## Hardening checklist (ship this to prod)


- **Eliminate upstream H1** where possible. Prefer **end-to-end HTTP/2** (or H3) so you don’t replay H2 as H1.
- Where H1 upstream is unavoidable:
- **Normalize once** (at the edge), then **strip** ambiguous headers before forwarding.
- Reject requests with **both** `Content-Length` and `Transfer-Encoding`.
- Forbid **duplicate** `Content-Length` or obs-fold whitespace.
- Disable or gate `Transfer-Encoding: chunked` to only endpoints that need streaming.
- Avoid back-end features that auto-read bodies without size caps.
- **Connection hygiene:** do not coalesce unrelated tenants over the same upstream connection. Tie request identity to connection identity where feasible.
- **Cache keys:** include relevant headers and the request line; prefer **cache slicing** to avoid cross-user poisoning.
- **Regression tests:** pin a corpus of malformed requests; assert identical behavior at both hops.


## Burp + tooling


- Burp Suite’s **Request Smuggler** extension automates many probes (H1/H2, downgrade, client-side desync). It’s great for triage and reproduction.
- Repeater + Logger++ give precise visibility into what each hop returned. Avoid Intruder-style floods.


## Case studies & reading list


- PortSwigger Academy — *What is HTTP request smuggling?* (intro + labs)
- https://portswigger.net/web-security/request-smuggling
- Advanced labs: https://portswigger.net/web-security/request-smuggling/advanced
- Research by James Kettle (PortSwigger):
- *HTTP Desync Attacks: Request Smuggling Reborn* (Black Hat/DEF CON 2019) — theory, case studies, defense
- *HTTP/2: The Sequel is Always Worse* (Black Hat 2021) — H2-exclusive threats and downgraders
- *Browser-Powered Desync Attacks* (2022) — client-side primitives
- *HTTP/1.1 Must Die: The Desync Endgame* (2025) — why upstream H1 remains dangerous
- Spec baseline: **RFC 9112** (HTTP/1.1 message syntax & framing)


## Appendix: Handy payload shapes (educational)


> These are **generic** shapes for lab environments. Never aim at real services without permission.


**CL.TE skeleton**


```
POST /lab HTTP/1.1
Host: example
Content-Length: 60
Transfer-Encoding: chunked


0\r\n\r\nGET /victim HTTP/1.1\r\nHost: example\r\n\r\n
```


**TE.CL skeleton**


```
POST /lab HTTP/1.1
Host: example
Content-Length: 4
Transfer-Encoding: chunked


5\r\nHELLO\r\n0\r\n\r\n
```


**CL.CL skeleton** (ambiguous duplicate)


```
POST /lab HTTP/1.1
Host: example
Content-Length: 4
Content-Length: 10


PING\r\nGET /victim HTTP/1.1\r\nHost: example\r\n\r\n
```


---


If you ship systems that still rely on upstream HTTP/1.1, your best defense is **simplify and normalize**: one parser, one policy, consistent framing. Otherwise, attackers will keep ending your HTTP for you.
