+++
date = '2026-10-26T09:15:06Z'
draft = true
---
title: "Cloudflare-registered .uk domain stuck in Pending (ns_mismatch) — registrar not syncing assigned nameservers to the .uk registry"
date: 2026-10-26
categories:
  - Registrar
  - DNS
draft: false
---

I have a .uk domain registered with Cloudflare Registrar whose zone is stuck in Pending Nameserver Update because the .uk registry delegation does not match the nameservers Cloudflare assigned to the zone. I'm on the Free plan, so I can't open a support case — posting here in case a Cloudflare staff member can help action the registrar-side sync.

**Domain:** focusedpathltd.co.uk  
**Zone ID:** eaeeb875d1b817cab091e123087e04bb  
**Registrar:** Cloudflare, Inc.  
**Zone created:** 2026-10-25 (under 24 hours ago)

## The mismatch

- **Cloudflare assigned (zone Overview):** `kaiser.ns.cloudflare.com`, `sonia.ns.cloudflare.com`
- **.uk registry (Nominet) still delegates to:** `hayes.ns.cloudflare.com`, `leanna.ns.cloudflare.com`
- **Zone status:** Pending, failure reason `ns_mismatch`

`kaiser` and `sonia` already answer authoritatively for the zone and return the new apex A record `81.2.182.40`. The zone is fully configured on the assigned pair — the only thing blocking activation is the .uk registry delegation still pointing at the old pair.

## Why this happened

The domain already delegated to Cloudflare (`hayes`/`leanna`) when the zone was (re-)added, so Cloudflare's anti-hijack logic assigned a different pair (`kaiser`/`sonia`). The .uk registry delegation was never updated to the new pair, so the zone can't activate.

## What I've tried / confirmed

- `dig +trace focusedpathltd.co.uk NS` → .uk delegation shows `hayes`/`leanna`
- `dig DS focusedpathltd.co.uk` → `ANSWER: 0` (no DS record — DNSSEC is not a factor)
- `dig @kaiser.ns.cloudflare.com focusedpathltd.co.uk A` → `81.2.182.40` (authoritative, correct)
- Cloudflare Registrar does not expose a nameserver editor (domains are locked to Cloudflare NS), so I can't set them myself.
- Nominet won't accept a registrant-side nameserver change — it must come via the registrar (Cloudflare).

## The ask

Could Cloudflare Registrar update the .uk registry (Nominet) delegation for `focusedpathltd.co.uk` from `hayes`/`leanna` to the assigned pair `kaiser`/`sonia`? Once the registry flips, the zone should activate immediately (the assigned pair already serves it).

Happy to provide any additional output (full `dig` traces, zone screenshots) on request. Thanks!

+++
