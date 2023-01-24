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

## Root/KVM Servers
- [Netcup](https://www.netcup.eu/vserver/) S-Tier root server provider. Good prices for decent hardware/uplink

## Webhosting
- [Netlify](https://www.netlify.com/) Amazing free tier plan for static websites
- [Vercel](https://vercel.com/) Free plan is okay. Would not recommend using them as backend for most projects

I am honestly not the biggest fan of these serverless cloud providers and running your backend on them.
Some downsides I have experienced:
- Vendor lock-in everywhere
- Vercel/Next.js tries to look bigger/more accpeted then they acutally are
	- Next.js backend api is a fucking joke
		- Middleware broken for years (now its acceptable)
		- Breaks compatibility with nearly every connect based library (its still build on connect tho i guess)
- Only advantage they provide is 'on the edge' hosting/cdn stuff
- Overpriced af. You are paying a lot for fancy UI and CI/CD. Both netlify and vercel are just reselling aws lambda as backend at the end of the day

// TODO: Add more providers when i remember them & add affiliate links
// TODO: Do a more serious post where i talk/rant about vercel/next.js
