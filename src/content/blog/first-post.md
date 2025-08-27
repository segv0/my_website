---
title: "LIT-CTF 2025 WriteUp"
description: "Some solutions of remarkable challenges from LIT-CTF"
pubDate: "Aug 27 2025"
heroImage: "/LIT_logo.png"
---

The LIT-CTF 2025 competition featured a diverse set of challenges spanning multiple categories, including web exploitation, cryptography, reverse engineering, and forensics. Each task was carefully designed to test not only technical proficiency but also creativity and problem-solving under pressure.

# 1) Web challenge:     web/group chat 2 |  49 solves / 198 points

In this challenge we are given a "main.py" file that we can run it on our local machine. And we examine the source code:



![Question_one](/questions/web_chat2_part1.png)
![Question_one](/questions/web_chat2_part1.png)



## The application is vulnerable because it uses render_template_string with untrusted user input. Chat messages are concatenated into an HTML string and then passed to Jinja2, which interprets any template syntax like {{ ... }}.

![Solution](/solutions/web_group2_sol.png)

 ## **This exploit script abuses the SSTI flaw in the chat app to first leak Flask’s SECRET_KEY and then forge a malicious session cookie. It does this by sending two crafted usernames that stitch together {{ config }}, which makes Jinja render the server’s config and reveal the secret key. With that key, the script signs a fake session where the username itself contains a Jinja payload that executes os.popen('cat flag.txt').read(). When the server renders the chat with this forged session, the payload runs server-side and the contents of flag.txt are returned in the chat log.**
