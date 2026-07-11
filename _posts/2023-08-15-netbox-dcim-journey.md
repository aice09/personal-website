---
layout: post
title: "NetBox: My Push for Better DCIM"
date: 2023-08-15
tags: [infrastructure, netbox, dcim]
---

August 2023 — for a few months leading up to this, I'd been quietly obsessed with a project called NetBox, a DCIM (Data Center Infrastructure Management) and IPAM tool. What started as curiosity turned into something I genuinely wanted to champion at work, even knowing it wouldn't be an easy sell.

## Getting close to the source

Instead of just reading documentation from a distance, I joined the NetBox community Slack and actually talked with the developers and the CEO directly. There's something energizing about being able to ask real questions to the people building the tool, rather than guessing from GitHub issues alone — understanding not just what NetBox does today, but where it's headed.

The more I learned, the more convinced I became that this was a tool worth bringing into our own environment. So I introduced it to our DC team — I'm part of the Asia DC team — as a candidate for how we could better track and manage our infrastructure inventory.

## Proving it out myself

I didn't want to just pitch an idea — I wanted to show it working. So I ran it in my own homelab first, getting hands-on with the setup before bringing it anywhere near production conversations. From there, I spun up some VMs in the office specifically so my team could see and interact with it themselves, rather than just hearing me describe it.

I also spent time populating device libraries — the definitions NetBox uses to represent specific hardware models — using the community-maintained device-type library as a starting base, so the tool would actually reflect real gear we work with instead of being an empty shell.

## Bringing it to the people who'd actually need to sign off

A good tool doesn't matter if it stays in a homelab. So I brought it to the people who'd actually be affected by adopting it — our IT Asset Manager and ServiceNow Manager, who's also part of the IT Asset Management team and our IT Security team based in the Philippines. I wanted their eyes on it early, not as an afterthought after a decision was already made.

Those conversations were genuinely useful. Both teams raised fair, grounded questions — about security posture, licensing, and how (or whether) NetBox could connect with our existing ServiceNow environment. When I looked into it, ServiceNow integration wasn't available yet at that point — a real gap, and a fair reason for caution.

## The part that stung a little

Here's the honest part of this story: not everyone on the team was ready to move. Some were hesitant to shift away from what we already had, even though I believed — and still believe — that NetBox was the better path forward for how we track and manage our infrastructure long-term.

That's a tough spot to be in. You do the research, you build the proof, you bring in the right stakeholders, and you still run into inertia. It's not because anyone was wrong to be cautious — the security, licensing, and integration questions were completely valid. But there's a difference between healthy caution and just staying comfortable with the familiar.

## Why I still believe in it

I didn't get the full adoption I was pushing for, at least not right away. But I don't think of that as wasted effort. I introduced a genuinely strong tool to the people who needed to know about it, I did the legwork to prove it could work in our environment, and I got real feedback from security and asset management that will matter whenever the conversation comes back around — and I do think it will.

Sometimes championing something good isn't about winning immediately. It's about putting in the groundwork, being the person who raises their hand and says "I think this is worth trying," and trusting that even if the answer today is "not yet," you've moved the conversation forward for whenever the timing is right. NetBox is still, in my view, going to be a great product — and I'd rather have been early and patient than never have brought it up at all.
