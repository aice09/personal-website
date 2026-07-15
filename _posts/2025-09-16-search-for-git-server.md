---
title: "My Search for a Self-Hosted Git Server"
date: 2025-09-16
tags: [selfhosted, homelab, git, gitea, gitlab, gogs, kubernetes]
---

Looking back, I never set out to become someone who has opinions about self-hosted Git servers. It started the way most homelab rabbit holes do — I just wanted a private place to push code that wasn't GitHub, something I fully owned, running on hardware I could reach out and touch. What followed was a slow tour through three very different philosophies of what a Git server should be, tried across a Windows Server box and an Ubuntu VM, before I landed somewhere I actually wanted to stay.

I wanted a place to push code that wasn't GitHub. That's really all this started as. No grand plan, just "I want repos I control, on my own hardware." What I didn't expect was to end up trying three completely different git servers before settling down.

## Gogs first

Gogs was the first one I tried, mostly because everyone describes it as lightweight — single binary, barely any footprint, up in minutes. And it is exactly that. Setup was painless on both Windows Server and later on an Ubuntu VM I spun up. No complaints there.

But it started feeling stale pretty fast. Releases are slow, the community's quiet, and I kept bumping into stuff that just... isn't there. It works fine as a bare-minimum git host, but I wasn't looking for bare minimum, I was looking for something I could actually build a workflow around.

## Then I went way too far the other way with GitLab

Next up, GitLab. Full send. CI/CD, container registry, issue boards, merge requests, the whole platform. It's genuinely impressive as a piece of engineering — I run OpenStack and Kubernetes at scale for work so I get why people build systems like this. But running it on a homelab made of Lenovo ThinkCentre M710q Tinys? Rough. It just wants more than these little boxes want to give. RAM usage alone made me close it down more than once. It's not that GitLab is bad, it's that I was using a sledgehammer to hang a picture frame.

## Gitea, and this is where I stayed

Gitea ended up being the one. It's actually forked from Gogs, so the lightweight feel is still there, but it didn't stagnate — regular releases, actions/CI support, package registry, active community, and a UI that feels finished instead of half-abandoned. I tried it on Windows Server first, then again on an Ubuntu VM, and both installs were quick enough that I basically forgot I was "testing" something and just started using it for real repos.

No single feature sold me on it. It just didn't get in my way, and it didn't ask for resources I don't have to spare. It's my main self-hosted git server now — anything I don't want sitting on GitHub lives there.

## Next up: Forgejo, and moving the whole thing into Kubernetes

Haven't tried Forgejo yet, but it's next on the list. It's the community fork of Gitea that split off to run its own governance, and from what I've read the config and feel are close enough to Gitea that switching shouldn't be painful. Low risk to test.

The bigger thing I actually want to do is get git hosting off a VM and into my bare-metal Kubernetes homelab cluster — same cluster I've been wrestling with on the Cilium/kube-vip/kubeadm side, fighting DaemonSet crashes and CIDR overlaps. Both Gitea and Forgejo have Helm charts with statefulset persistence and external Postgres support, so once the cluster itself stops being on fire, moving git hosting in should mostly be a matter of doing it properly — PVCs instead of a VM disk, actual resource limits, ingress instead of "just hit the IP."

We'll see how Forgejo goes. Might replace Gitea entirely, might not. Either way it's one more thing running on infra I built myself instead of a VM I forgot to snapshot.
