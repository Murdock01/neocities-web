+++
date = '2025-10-24T15:13:02+02:00'
draft = false
title = 'Glassworm'
tags = ['IT-Security']
+++
# Glassworm – A New Earthquake in the IT Landscape?
### A new piece of malware has been spreading through the developer community in recent days. The discoverers, the Israeli-American cybersecurity firm KOI, have named it “Glassworm” — a nod to its ability to hide using ***invisible code***.  
https://www.koi.ai/blog/glassworm-first-self-propagating-worm-using-invisible-code-hits-openvsx-marketplace

The malware known as *Glassworm* is not just a simple worm — it represents one of the most advanced software supply chain attacks ever seen.  
It spreads via extensions for the widely used code editor VS Code and its derivatives (such as VS Codium).  
Glassworm employs a mix of advanced techniques that, in combination, are unprecedented in malware.

- **Invisible code** in affected extensions using non-renderable Unicode characters. These hinder manual code reviews, making the malicious code practically *invisible*. The worm uses [Unicode block variant selectors](https://en.wikipedia.org/wiki/Unicode_block_variant_selectors).  
  “This technique breaks traditional code review completely,” said Idan Dardikman, researcher at KOI Security. “You can’t detect what you can’t see.”
- **Payload retrieval via Solana blockchain transactions.** Once recorded, these transactions cannot be deleted and persist as long as the blockchain exists. Each transaction contains a reference to the C&C server hosting the next-stage payload. If the server is blocked or seized, the malware operators simply create a new transaction pointing to a different server. The malware always seeks the most recent transaction.
- **Solana transactions are extremely cheap**, costing between $0.0001 and $0.0025 USD.
- **Google Calendar entries** serve as a backup channel, which are difficult to block in corporate networks without disabling calendar functionality entirely.
- The actual payload is downloaded from the C&C server and is encrypted using **AES-CBC-256**.  
  This payload includes several advanced capabilities:
  - Harvests NPM, GitHub, and OpenVSX credentials
  - Targets browser wallet extensions (49 wallet types listed)
  - Sets up SOCKS proxies
  - Installs hidden VNC services for remote access
  - Uses peer-to-peer mechanisms like WebRTC and BitTorrent-DHT for decentralized command distribution

- In the final stage — ironically named “ZOMBI” by both the discoverers and the malware authors — Glassworm turns developer machines into persistent, hard-to-detect infrastructure nodes. These can be used by attackers to access internal networks or anonymize transactions.

## Automated Propagation

The malware searches infected systems for credentials to various developer platforms and uses npm auth tokens to propagate via the JavaScript package manager. It also looks for GitHub tokens and Git credentials to compromise additional repositories. Access credentials for OpenVSX are also targeted to infect more VS Code extensions.

The [KOI blog post](https://www.koi.ai/blog/glassworm-first-self-propagating-worm-using-invisible-code-hits-openvsx-marketplace) includes a list of known infected extensions. While the list currently contains mostly obscure packages, it’s only a matter of time before Glassworm reaches more popular ones.

This supply chain attack clearly demonstrates the potential of using legitimate decentralized services to build a C&C infrastructure that cannot be easily shut down.

The attack has the potential to surpass the impact and reach of other well-known threats like Shai Hulud.  
Now, the developer community and operators of software marketplaces and code repositories must find effective countermeasures — such as 2FA, granular access tokens for repositories, and “Trusted Publishing.”

The situation remains highly dynamic.