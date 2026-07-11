---
layout: post
title: "The Beauty of RAID CLI and iLO: Chasing Down a Disk That 'Looked Fine'"
date: 2026-05-15
---

There's a particular kind of frustration in staring at a healthy-looking dashboard while something underneath it is clearly not healthy. That's basically the summary of a good chunk of my storage troubleshooting work on our IaaS layer — and it's also where I learned to actually trust the logs over the pretty status page.

## The setup: when Ceph says something's wrong

Working on IaaS infrastructure with Ceph as the shared storage backend, one of the recurring questions I have to answer whenever something looks off is deceptively simple to ask and genuinely hard to answer: **is this a disk problem, a host problem, or just noise?**

Ceph will tell you an OSD is flapping, slow, or down. What it won't tell you outright is *why* — that part means going a layer deeper into the actual hardware underneath the OSD.

## The toolkit

Over time I built up a real workflow for this, mostly leaning on:

- **`smartctl`** — pulls SMART data directly off the disk: reallocated sector counts, pending sectors, uncorrectable errors, drive temperature history. This is usually the first place a disk starts confessing it's dying, often well before it fails outright.
- **`dmesg`** — kernel-level messages, great for catching I/O errors, SCSI/SATA reset events, or controller-level complaints happening in real time.
- **`journalctl`** — systemd's journal, useful for correlating disk errors with service-level symptoms — did the OSD process crash right when the kernel logged a read error? That correlation is often the smoking gun.
- **Disk error codes** — reading through actual SCSI/ATA sense codes instead of just "disk error" gives you the difference between a media error, a timeout, and a controller-reported fault — each one points you somewhere different.
- **RAID CLI (logical and physical views)** — this is the piece a lot of people skip. The RAID controller's own CLI tool lets you inspect both the logical drive (what the OS sees) and the physical drive (what's actually installed) separately. A logical volume can report healthy while a specific physical disk underneath it is throwing predictive failure flags — and that gap is exactly where a lot of "everything looks fine but it's not" cases live.
- **iLO** — HPE's out-of-band management interface, useful for a hardware-level health summary without needing OS access at all. Great for a first pass, but as I found out, not always the full picture.

## The disk that "looked fine"

Here's the case that really drove the lesson home. I was troubleshooting a host with a suspicious OSD, and iLO reported the disk status as fine — green light, no fault flagged, nothing alarming in the summary view.

But the RAID CLI physical disk view and the kernel logs told a different story. `smartctl` showed climbing reallocated sector counts. `dmesg` had scattered I/O errors tied to that specific disk. The RAID controller's own physical disk status, read directly rather than through iLO's summary layer, showed predictive failure indicators that iLO simply wasn't surfacing.

That gap — hardware management interface says fine, actual logs and controller-level data say otherwise — is a real trap if you only ever check the dashboard. iLO aggregates and simplifies; it doesn't always propagate every signal a drive is generating at the SMART or controller level.

## Pushing for the replacement anyway

Armed with the RAID CLI physical disk read and the SMART/log evidence, I made the case to DC support to get the disk replaced through the vendor — even though the "official" hardware management view said everything was okay. That's not always an easy sell; vendors and DC support teams naturally lean on the tool that's supposed to be authoritative for hardware health. But bringing actual SMART counters, kernel error logs, and RAID controller output made the case concrete instead of "I have a feeling this disk is bad."

The replacement went through, and it reinforced something I now treat as a rule rather than a suggestion: **the management UI is a summary, not the ground truth.** If the story from `smartctl`, `dmesg`, `journalctl`, and the RAID CLI's physical view all point the same direction, that's the signal to trust — even if the fancy dashboard is still showing green.

## Why I actually enjoy this part of the job

There's something genuinely satisfying about this kind of troubleshooting. It's detective work with hard evidence instead of guesses — cross-referencing SMART counters against kernel timestamps against RAID controller state against Ceph's own OSD health signals, until the pieces line up into one coherent story instead of four separate half-answers.

It's also a good reminder that infrastructure health isn't one number on one screen. It's a stack of signals — application layer, storage layer, kernel layer, controller layer, hardware layer — and the real skill is knowing which layer to check when the one you're looking at isn't giving you the full picture.

That's the beauty of RAID CLI and iLO, honestly — not that either one is perfect on its own, but that between the two of them, plus the OS-level tools sitting alongside them, you can actually reconstruct what's really happening on a disk that's trying very hard to look fine.
