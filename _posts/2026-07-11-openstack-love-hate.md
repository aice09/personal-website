---
layout: post
title: "Why I Keep Coming Back to OpenStack"
date: 2026-07-11
---

If you'd told me a year ago how much time I'd spend staring at RabbitMQ logs at 2 AM, I probably would've laughed. But here I am, and honestly — I wouldn't trade it.

## The hard part

OpenStack is not a gentle system. It's layered, it's distributed, and when something breaks, it rarely breaks in isolation. A single Mnesia state loss in RabbitMQ can cascade into Neutron agents going dark and Nova compute services dropping out simultaneously — and suddenly what looked like "one queue had an issue" is actually half your control plane looking unhealthy at once.

I've spent hours chasing MariaDB Galera split-brain scenarios, trying to figure out which node actually holds the safe state to bootstrap from — running `wsrep_recover`, checking `safe_to_bootstrap` flags, hoping I picked the right node before forcing a new cluster into existence. Get it wrong, and you're rebuilding from a backup instead of a quick recovery.

Then there's the networking layer. Neutron L3 agents and OVS agents behave differently when they're running inside Kubernetes pods instead of on bare metal — the usual troubleshooting playbook doesn't fully apply, so you end up building a new mental model for how the same components fail in a containerized context.

I've also dealt with a dual SSD failure — correlated RAID 1 wear, both drives dying close together — that took down VMs running local qcow2 ephemeral storage. No shared storage to fall back on for those workloads, so it meant forcing state resets on the stuck instances and evacuating them to another host, then writing up an incident summary for the hardware team afterward. And on the Kubernetes side underneath all of it, I've had to recover a control plane after losing a master node — watching etcd leadership flap between the two remaining members before quorum settled back down.

None of these problems live in a single layer. That's the part that makes OpenStack uniquely exhausting to operate.

## Why it's fascinating anyway

That's exactly what pulls me in, though. OpenStack forces you to understand *systems*, not just tools. You can't troubleshoot Neutron without understanding how it talks to Nova through the message bus. You can't fix a compute service outage without understanding what RabbitMQ and Mnesia are doing underneath it. Every incident is a lesson in distributed systems design — quorum, state consistency, failure domains — playing out in real infrastructure instead of a textbook diagram.

There's a specific kind of satisfaction in finally tracing a cascading failure back to its actual root cause. Not the symptom — Nova compute showing DOWN, Neutron agents unresponsive — but the actual originating event, like a single Mnesia table going corrupt. It's the same feeling as solving a hard puzzle, except the puzzle is also serving real workloads, and getting it right means someone's VM comes back online.

Migrations are another place where the complexity turns into something almost elegant when it works. Block migration for VMs on local ephemeral storage — moving a running instance's disk state across hosts without shared storage — feels like it shouldn't be possible until you watch it actually happen cleanly.

## What keeps me going

I think what keeps me coming back isn't that OpenStack is easy — it's clearly not. It's that every time I fix something, I understand the whole stack a little better. Bare metal, host Kubernetes, the OpenStack control plane, tenant clusters running inside OpenStack VMs, Ceph underneath all of it as shared storage — it's a lot of moving parts, but each incident makes the overall picture clearer than the last one did.

If you're starting out with OpenStack and it feels overwhelming — it is. But that difficulty is also where the actual learning lives. Stick with it long enough, and the moments where everything clicks into place, where you finally see how the message bus, the database, the network agents, and the compute layer all fit together, make all the 2 AM debugging worth it.
