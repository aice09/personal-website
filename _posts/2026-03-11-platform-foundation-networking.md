---
layout: post
title: "The ARP Bug, the Upgrade, and What They Taught Me About Kubernetes Networking"
date: 2026-03-11
---

Looking back at something that happened in the second half of last year, during my probationary period — I was still new to Kubernetes when I got pulled into a bug that just wouldn't repeat itself. It would show up, vanish, and leave me questioning my own logs, over and over, until I started doubting whether I even understood our platform foundation module — MetalLB, ExternalDNS, and Emissary-Ingress — at all. Turns out the bug came wrapped in ARP packets and a subnet nobody had double-checked.

That project taught me more about how networking actually works than anything I'd read beforehand, and a lot of that credit goes to a colleague who was patient enough to walk through the concepts with me while we were both under deadline pressure. Being new during a probationary period made it feel higher stakes than it probably needed to be. But that pressure is also what pushed me to actually understand the pieces instead of just copying configs that "worked" — because it usually takes a good scare, or a messy upgrade, to close that gap between configuring something and understanding it.
## The three pieces, and how they actually connect

**MetalLB** solves a problem bare-metal Kubernetes clusters have that cloud-managed clusters don't: there's no cloud load balancer to hand out external IPs. MetalLB fills that gap by advertising IP addresses from a pool you define, using either Layer 2 mode (ARP/NDP) or BGP. When you create a `Service` of type `LoadBalancer`, MetalLB is the thing that actually assigns it a real, routable IP and makes the network aware that IP lives on a specific node.

**Emissary-Ingress** (built on the Ambassador API Gateway/Envoy) sits above that, handling actual HTTP/HTTPS routing — host-based routing, TLS termination, path rewriting, rate limiting. It reads Kubernetes annotations on `Mapping` and `Host` custom resources to configure Envoy's routing rules dynamically.

**ExternalDNS** closes the loop. It watches your Ingress/Service resources and automatically creates matching DNS records in an external provider — in our case, that meant connecting to AWS (Route53), depending on which zone a given domain lived in. So the full chain looks like: MetalLB gives a Service a real IP → Emissary-Ingress routes traffic to the right backend based on hostname → ExternalDNS notices the Ingress and creates a DNS record pointing at that IP, all without a human manually touching DNS.

I spent real time studying how these three pieces actually interconnect — not just what each one does on its own, but how a change in one ripples into the others. The way I eventually explained it to myself was like a mall: MetalLB is the mall assigning your store a physical address in the building. Emissary-Ingress is the directory board and the hallways — it decides which door a visitor gets routed through based on which store they're actually looking for. ExternalDNS is the signage outside on the street, automatically updating so people searching for your store from outside the building actually get pointed to the right entrance. None of the three do much alone. The value is entirely in how they hand off to each other.

That mental model is what let me properly prepare the RCA for the incident below and deliver it in a way the team could actually follow, instead of just listing "I found a bug" without explaining the mechanism behind it.

## Second half of last year: the ARP bug that taught me networking

Back in the second half of last year during my probationary period, I was pulled into investigating something stranger: an intermittent, fluctuating connectivity issue across our dev and staging clusters. Services would be reachable, then suddenly not, then reachable again, with no clear pattern. Nothing in the application logs pointed anywhere useful. It looked like a phantom problem.

I was still new to Kubernetes at the time, and figuring this out took real digging — with a lot of help from a colleague who was patient enough to walk through concepts with me while we were both under pressure.

I went deep into MetalLB's Layer 2 mode internals to understand what was actually happening. MetalLB in L2 mode works by having one node ARP-announce ownership of a given service IP on the local network segment — it's literally answering "who has this IP" with "I do," the same way any host claims an IP on a LAN.

Digging further, our team found the actual root cause: our dev and staging clusters were configured with MetalLB IP address pools drawn from the **same subnet**, and both clusters were sitting on the same underlying network segment. Whoever had provisioned the second cluster had missed narrowing the IP range, so both clusters had overlapping address space to hand out. That meant two different nodes — one in dev, one in staging — could end up both believing they owned the same IP, and would both send ARP replies claiming it. Depending on which reply won the race at any given moment, traffic would go to one cluster or the other, which is exactly the "sometimes works, sometimes doesn't" symptom we were chasing.

Once we understood that ARP was the mechanism actually being fought over, the fix was straightforward: correct the IP pool ranges so dev and staging had properly separated, non-overlapping subnets. I used the mall analogy above to explain the whole chain in the RCA — two stores had accidentally been assigned the same address, so visitors kept getting routed to whichever one answered first. It landed well with the team, and it was one of the first times I felt like I could take something genuinely confusing and make it click for other people too.

## Q2 this year: upgrading the platform module

This quarter, I moved from investigating a networking bug to owning an actual upgrade of the platform foundation module — and the biggest lesson here was completely different: how much of a safe upgrade actually depends on documentation discipline, not just running a new version.

I learned to slow down and actually read release notes and upgrade guides properly — what changes between versions, what's now required that used to be optional, what breaking changes exist, and what the migration path actually looks like before touching anything.

**MetalLB** turned out to be the clearest lesson in "read the docs first." We were running the Bitnami-packaged MetalLB chart, and moving to the upstream `metallb/metallb` chart wasn't a simple in-place upgrade — the two aren't structured the same way, and trying to upgrade over the top of the old install risked leaving conflicting resources behind. The only safe path was a full uninstall of the Bitnami version and a clean install of the upstream chart. Knowing that ahead of time, instead of finding out mid-upgrade, saved us from a much messier cleanup.

**Emissary-Ingress** turned into the most complex piece. During a POC upgrade in the dev cluster, we found a lot more Services were affected than we expected — the newer version required annotations to move to the `getambassador.io/v3alpha1` API, which meant every `Mapping` and `Host` resource using the old annotation format needed updating. For services we owned directly — logging, and a few other internal tools — we went ahead and updated the annotations ourselves. But several affected services belonged to other teams, so that part is still pending coordination with them before we can move forward cluster-wide. It was a good reminder that a platform upgrade isn't just a technical task — it's also a communication and sequencing problem across teams.

**ExternalDNS**, by contrast, was refreshingly smooth. We found it had no dependency conflicts blocking a direct jump — we could simply uninstall the older version and install the latest release cleanly, without needing to step through every intermediate version one at a time. Not every upgrade needs to be complicated, and it was a nice contrast to see that confirmed directly instead of just assuming the worst going in.

## Why both experiences stuck with me

Two very different problems — one a quiet, slow-burn investigation into a networking collision, the other a structured, cross-team version upgrade — but both forced the same kind of learning. You can't safely operate MetalLB, ExternalDNS, or Emissary-Ingress by treating them as black boxes. You have to understand what's happening underneath: ARP announcements, chart architecture differences, annotation contracts between resources, and who else depends on the thing you're about to change.

I went into last year only knowing Kubernetes basics — Services, Pods, Deployments — and came out understanding how load balancer IP advertisement, DNS automation, and ingress routing all interlock as one system, well enough to explain it with something as simple as a mall analogy. And I got there because I had colleagues willing to explain the pieces and sanity-check my theories along the way. Being new doesn't mean you can't dig into the hard stuff — it just means it's more fun with someone next to you.

That's still the thing I love about this work. Every "weird intermittent bug" or "this upgrade touches more than we thought" is really just a system telling you exactly what's connected to what, in a language you haven't fully learned yet. You just have to be willing to go down the rabbit hole — and sometimes pick up the phone to another team — until it all makes sense.
