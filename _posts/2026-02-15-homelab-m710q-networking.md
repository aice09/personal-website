---
layout: post
title: "Homelab Lessons: The M710q, Realtek Adapters, and Why pfSense Wants Intel"
date: 2026-02-15
---

Homelabbing teaches you things production environments never will — mostly because you're allowed to fail loudly and often, on hardware you own, without a change ticket in sight. One of my favorite (and most frustrating) lessons came from something as "simple" as networking on a Lenovo ThinkCentre M710q.

## The setup

I picked up 5 M710q units to build this out — one dedicated as a firewall box, and four to run the actual infra workloads. A mix of sources: some bought online, some picked up secondhand from a colleague. I also grabbed a Cisco switch to tie everything together properly instead of relying on a consumer-grade switch. Small footprint, low power draw, and enough of them to actually simulate a real multi-node environment instead of just one box doing everything.

## The M710q and the NVM checksum problem

One of the units had an issue with its onboard Intel Ethernet controller: a corrupted NVM (non-volatile memory) checksum. The network adapter would report checksum errors, and depending on the exact failure mode, that can mean anything from cosmetic warnings to the NIC refusing to negotiate properly.

Repairing an NVM checksum on Intel controllers isn't something most people ever have to think about — until suddenly it's the reason your homelab node won't get a reliable link. It pushed me to actually learn how Intel's NIC firmware and NVM structures work, instead of just treating the network port as a black box that either works or doesn't.

## Realtek adapters: fine, until they aren't

At some point I swapped in a USB-based adapter using a Realtek chipset, mostly out of convenience — they're cheap and common. And it mostly worked. Mostly.

The frustrating part is that Realtek reliability seems to depend entirely on what you're trying to run on top of it. Some workloads and apps handle Realtek's driver quirks fine. Others — particularly anything sensitive to specific offload features, driver stability, or consistent throughput — would flake out in ways that took real time to diagnose. It's the kind of problem that doesn't show up in a spec sheet: the chipset "works," right up until the specific thing you're deploying exposes exactly the edge case it doesn't handle well.

## pfSense and its opinion about your NIC

This is where things got clearest. When I set up pfSense as the firewall on one of the M710q boxes, I learned quickly that it has a strong preference for Intel network adapters. Realtek chipsets are notorious in the pfSense community for inconsistent driver support — dropped packets, poor performance under load, or outright instability, especially compared to Intel's NICs, which have mature, well-tested drivers across FreeBSD (which pfSense is built on).

So the same Realtek adapter that was "good enough" for one use case became a real liability the moment pfSense was the thing depending on it. That's the pattern that stuck with me: reliability isn't a fixed property of hardware, it's a property of hardware paired with a specific workload.

## The actual lesson

None of this was fun in the moment. Chasing an NVM checksum issue, or debugging why a Realtek NIC works under one app but chokes under another, isn't glamorous work. But it's exactly the kind of hands-on hardware/network troubleshooting that builds real intuition — the kind you don't get from reading a datasheet.

It's also just a good reminder: in a homelab or in production, "network adapter" is never really one thing. It's a chipset, a driver, a firmware revision, and the specific software stack riding on top of it — and any one of those layers can be the reason something that should just work, doesn't.

That's the whole appeal of the homelab, honestly. Five little boxes, a Cisco switch, and enough networking headaches to actually understand why things break — you get to hit these walls on your own hardware, on your own time, and come out the other side actually understanding it.
