---
layout: post
title: "Making Sure Nothing Leaves Exposed: A ShredOS POC Story"
date: 2024-10-15
tags: [infrastructure, compliance, security]
---

Last quarter of the year — and one of the items on our plate was IT asset disposal. A backlog of old hardware needed to go, including a lot of drives that had been sitting around well past their useful life. The plan was straightforward on paper: hand them off to a vendor for physical disposal or shredding. Some disks had already been dismantled, with plenty more still waiting to be processed.

But "hand it off to a vendor" isn't where the responsibility ends. Before anything left our hands, we needed to make sure our side of the job was done first — the data itself had to be securely wiped, so there was zero exposure even before the physical destruction step ever happened. You don't get to assume a vendor's shredder is your only line of defense; sanitization needs to happen on your end, first.

## Finding the right tool

That's where **ShredOS** came in. It wasn't something we built — it's an existing, free, open-source tool, one that lines up with NIST's guidance on secure media sanitization. The team looked at it as a solid option that fit what we actually needed: boot it from USB, point it at a drive, and it handles secure overwriting without requiring us to build anything custom.

Before rolling it out across the whole disposal backlog, we ran a proper POC first — no point trusting a new tool at scale without validating it actually does what it claims.

## Running the POC

I set up four host machines for the proof of concept and configured them to run the wipe process, with reporting built in so every wipe had a record attached to it — which drive, which method, and confirmation that it completed. Once the setup was solid, I shared the results with the IT asset management team.

The output was successful across the board. Drives wiped cleanly, reports generated as expected, and the team had what it needed to sign off. That gave us the green light to proceed with rolling ShredOS out across the rest of the disposal backlog before those drives went to the vendor.

## Why this mattered

It's easy to treat asset disposal as an afterthought — old hardware, low priority, just get rid of it. But this project was a good reminder that disposal is still a data-handling process, not just a logistics one. Every one of those drives had, at some point, held real data, and the only way to guarantee it stayed protected was to verify the wipe ourselves instead of assuming the destruction step downstream would cover it.

It was also a nice change of pace from day-to-day operations. Instead of keeping something running, the goal here was making sure something was properly, verifiably gone — different mindset, same level of care.

## The takeaway

Sometimes the most important infrastructure work isn't about building something new — it's making sure something old is retired the right way, with the evidence to back it up.
