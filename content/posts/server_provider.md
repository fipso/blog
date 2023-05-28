---
title: Good server/hosting providers I know
description: Short out of context list of some linux server and static hosting providers I have experience with
date: 2023-01-24T14:30:00+02:00
draft: false
tags: [linux, server, hosting, web dev]
---

## vServers
- [OVH](https://www.ovhcloud.com/en/vps/) High quality vps with reasonable prices
- [Noez](https://noez.de/en) German server provider for cheap vServers (with pretty cheap extra IPv4 addresses)
- [Contabo](https://contabo.com/en) Another german vServer provider with cheap storage servers
- [Signaltransmitter](https://signaltransmitter.de/en/) Very cheap vps and even cheaper ipv6 only servers
- [1984.is](https://1984.hosting/) Privacy focused vServers in iceland

## Root/KVM Servers
- [OVH](https://www.ovhcloud.com/en/bare-metal/prices/) Affordable bare metal servers + storage. Decent for ETH Nodes
- [Netcup](https://www.netcup.eu/vserver/) S-Tier kvm server provider. Good prices for decent hardware/uplink
- [Hetzner](https://www.hetzner.com/dedicated-rootserver/matrix-ax) Has tons of cheap dedicated servers. Hetzner also provides a cloud service + API
- [Host.ag](https://www.host.ag/) Affordable bare metal in Sofia (Bulgaria)

## Webhosting
- [Netlify](https://www.netlify.com/) Amazing free tier plan for static websites
- [Vercel](https://vercel.com/) Free plan is okay. Would not recommend using them as backend for most projects

I am honestly not the biggest fan of many serverless cloud providers and running your backend on them.
Some downsides I have experienced:
- Vendor lock-in everywhere
- Only advantage they provide is 'on the edge' hosting/cdn services & management features
- Overpriced af. You are paying a lot for fancy UI and CI/CD. Both netlify and vercel are just reselling aws lambda as backend at the end of the day  

// TODO: Add more providers when i remember them & add affiliate links  
// TODO: Do a more serious post where i talk/rant about vercel/next.js  

Change Log:
- 28.05.2023 22:30 GMT+2: Rewrite serverless rant & add more providers
