---
layout: post
title: "My Search for a Self-Hosted Git Server"
date: 2025-09-16
tags: [selfhosted, homelab, git, gitea, gitlab, gogs, kubernetes]
---

Looking back, I never set out to become someone who has opinions about self-hosted Git servers. It started the way most homelab rabbit holes do — I just wanted a private place to push code that wasn't GitHub, something I fully owned.

## Starting with Gogs

Gogs was the first one I tried, mostly because everyone describes it as lightweight — single binary, barely any footprint, up in minutes. And it is exactly that. Setup was painless on both Windows Server and later on an Ubuntu VM I spun up. No complaints there.

But it started feeling stale pretty fast. Releases are slow, the community's quiet, and I kept bumping into stuff that just... isn't there. It works fine as a bare-minimum git host, but I wasn't looking for bare minimum, I was looking for something I could actually build a workflow around.

## Going big with GitLab

If Gogs was minimal, GitLab was the opposite extreme — CI/CD pipelines, container registry, issue boards, merge request workflows, the whole DevOps platform in a box. But running it at home made the tradeoff obvious fast. GitLab wants resources — real memory, real CPU, a database and a handful of background services all working in concert — and on a homelab built around Lenovo ThinkCentre M710q Tiny units, that's a lot to ask for a system whose only job, most days, was hosting a handful of personal repos. It felt like bringing a production platform to do a hobby's job.

## Gitea, and this is where I stayed

Gitea sat right in the middle, and that's exactly where I needed something to be. It's actually forked from Gogs, so the lightweight feel is still there, but it didn't stagnate — regular releases, actions/CI support, package registry, active community, and a UI that feels finished instead of half-abandoned. I tried it on Windows Server first, then again on an Ubuntu VM, and both installs were quick enough that I basically forgot I was "testing" something and just started using it for real repos.

It was the balance. Light enough to run comfortably on modest hardware, capable enough that I'm not missing anything GitLab offered for my use case, and maintained actively enough that I trust it as a long-term home for my repos rather than a placeholder. It's now my main self-hosted Git server, and every project I care about keeping outside of GitHub lives there.

## What's next: Forgejo, and taking it into Kubernetes

Haven't tried Forgejo yet, but it's next on the list. It's the community fork of Gitea that split off to run its own governance, and from what I've read the config and feel are close enough to Gitea that switching shouldn't be painful. Low risk to test.

The bigger thing I actually want to do is get git hosting off a VM and into my bare-metal Kubernetes homelab cluster — same cluster I've been wrestling with on the Cilium/kube-vip/kubeadm side, fighting DaemonSet crashes and CIDR overlaps. Both Gitea and Forgejo have Helm charts with statefulset persistence and external Postgres support, so once the cluster itself stops being on fire, moving git hosting in should mostly be a matter of doing it properly — PVCs instead of a VM disk, actual resource limits, ingress instead of "just hit the IP."

We'll see how Forgejo goes. Might replace Gitea entirely, might not. Either way it's one more thing running on infra I built myself instead of a VM I forgot to snapshot.
