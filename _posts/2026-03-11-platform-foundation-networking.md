---
layout: post
title: "Platform Foundation Deep Dive: MetalLB, ExternalDNS, Emissary-Ingress, and the ARP Bug That Taught Me Kubernetes Networking"
date: 2026-03-11
---

Looking back at something that happened in the second half of last year, during my probationary period — I was still new to Kubernetes when I got pulled into rebuilding our platform foundation module: MetalLB, ExternalDNS, and Emissary-Ingress. That project taught me more about how networking actually works than anything I'd read beforehand, and a lot of that credit goes to a colleague who was patient enough to walk through the concepts with me while we were both under deadline pressure.

Being new during a probationary period made it feel higher stakes than it probably needed to be. But that pressure is also what pushed me to actually understand the pieces instead of just copying configs that "worked."

## The three pieces, and how they actually connect

**MetalLB** solves a problem bare-metal Kubernetes clusters have that cloud-managed clusters don't: there's no cloud load balancer to hand out external IPs. MetalLB fills that gap by advertising IP addresses from a pool you define, using either Layer 2 mode (ARP/NDP) or BGP. When you create a `Service` of type `LoadBalancer`, MetalLB is the thing that actually assigns it a real, routable IP and makes the network aware that IP lives on a specific node.

**Emissary-Ingress** (built on the Ambassador API Gateway/Envoy) sits above that, handling actual HTTP/HTTPS routing — host-based routing, TLS termination, path rewriting, rate limiting. It reads Kubernetes annotations on `Mapping` and `Host` custom resources to configure Envoy's routing rules dynamically.

**ExternalDNS** closes the loop. It watches your Ingress/Service resources and automatically creates matching DNS records in an external provider — in our case, that meant connecting to both AWS (Route53) and Cloudflare, depending on which zone a given domain lived in. So the full chain looks like: MetalLB gives a Service a real IP → Emissary-Ingress routes traffic to the right backend based on hostname → ExternalDNS notices the Ingress and creates a DNS record pointing at that IP, all without a human manually touching DNS.

## Where annotations get tricky

This is the part that took real time to internalize. Each of these three tools reads its own set of annotations, and they have to line up correctly across resources or things silently misbehave instead of throwing a clear error.

- MetalLB uses annotations/config to pick which IP pool a `LoadBalancer` Service draws from
- ExternalDNS reads annotations like the hostname annotation on an Ingress/Service to know what DNS record to create, and which provider/zone to target
- Emissary-Ingress annotations configure routing behavior on the gateway itself — things like TLS context, load balancing policy, timeouts

When we upgraded Emissary-Ingress (the Ambassador-based gateway) to a newer version, some annotation behavior changed between versions — certain fields that used to be implicit needed to be explicit, and the interaction between the gateway's routing config and the underlying Service/Ingress objects shifted slightly. That's the kind of thing you only catch by actually reading the changelog carefully and testing in a lower environment first, which is exactly what we did.

## The bug that forced me to learn ARP

Here's the part I'm most proud of. After a change to the platform module, we started seeing an intermittent, fluctuating connectivity issue — services would be reachable, then suddenly not, then reachable again, with no clear pattern. Nothing in the application logs pointed anywhere useful. It looked like a phantom problem.

I went deep into MetalLB's Layer 2 mode internals to figure out what was actually happening. MetalLB in L2 mode works by having one node ARP-announce ownership of a given service IP on the local network segment — it's literally answering "who has this IP" with "I do," the same way any host claims an IP on a LAN.

Digging further, I found the actual root cause: our dev and staging clusters were configured with MetalLB IP address pools drawn from the **same subnet**, and both clusters were sitting on the same underlying network segment. Whoever had provisioned the second cluster had missed narrowing the IP range, so both clusters had overlapping address space to hand out. That meant two different nodes — one in dev, one in staging — could end up both believing they owned the same IP, and would both send ARP replies claiming it. Depending on which reply won the race at any given moment, traffic would go to one cluster or the other, which is exactly the "sometimes works, sometimes doesn't" symptom we were seeing.

Once I understood that ARP was the mechanism actually being fought over, the fix was straightforward: correct the IP pool ranges so dev and staging had properly separated, non-overlapping subnets. But getting there meant actually learning how L2 announcement works under the hood, instead of treating MetalLB as a black box that "just gives you an IP."

## Why this stuck with me

I delivered the fix and the root cause writeup, and it felt like a real turning point for me early in my Kubernetes journey. I went in only knowing the basics — Services, Pods, Deployments — and came out understanding how load balancer IP advertisement, ARP, DNS automation, and ingress routing all interlock as one system.

What made it work wasn't just persistence — it was having a colleague who was willing to explain the pieces and sanity-check my theory before I went and told the team "I think I found it." Being new doesn't mean you can't dig into the hard stuff. It just means you dig in with someone next to you, and it makes it fun instead of just stressful.

That's still the thing I love about this work. Every "weird intermittent bug" is really just a system telling you exactly what's wrong, in a language you haven't learned yet. You just have to be willing to go down the rabbit hole until it starts making sense.
