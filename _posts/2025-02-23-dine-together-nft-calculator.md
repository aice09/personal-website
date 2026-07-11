---
layout: post
title: "Dine Together: What a Small NFT Game Calculator Taught Me"
date: 2025-02-23
tags: [javascript, webdev, personal]
---

Not every meaningful project starts with a grand plan. Some of the best ones start small, almost by accident — you're just trying to solve one annoying little problem, and you don't realize until much later how much it actually taught you.

Mine started because I was playing an NFT restaurant simulator game called Dine Together, built on the FactoryChain network, and got tired of doing math in my head every time I wanted to figure out tips, ingredient costs, or whether a piece of furniture was actually worth the price.

So I built a calculator. Then another. Then a bunch more.

## From one page to a whole toolkit

Looking at the repo now, it's genuinely fun to see how far it grew from "let me just calculate my tips." There's a regular tip calculator, a bonus tip calculator, a combined tip-with-bonus calculator, a chef checker, a farming calculator (with a v2 after I clearly wasn't happy with the first version), a furniture info page, a dish calculator, marketplace pages for chefs and land, an exchange rate calculator, an energy calculator, and a synergy calculator. What began as a single utility page turned into a small suite of tools covering nearly every mechanic in the game.

That's one of the things I love about side projects like this — you rarely plan the scope up front. You just keep noticing "oh, I could calculate that too," and before long you've built something a lot bigger than you set out to make.

## What JavaScript quietly taught me

Most of these calculators are plain HTML and JavaScript — no framework, no build step, just straightforward DOM manipulation and math. That constraint turned out to be a gift. It's easy to reach for a framework by default, but building these reminded me how much you can accomplish with vanilla JS when the problem is well-scoped: read some inputs, run the formula, update the page. No React needed, no over-engineering — just solving the actual problem in front of me.

It also meant I got comfortable organizing a growing set of independent-but-related pages without everything turning into spaghetti — a skill that ended up mattering a lot more later than I expected.

## Where the curiosity about databases began

Somewhere along the way, a `server.js` and `package.json` showed up in the repo — a sign that static calculators weren't enough anymore, and I'd started poking into the idea of a small backend and persistent data. That's really where my curiosity about databases began in a hands-on, practical way: how you'd store game data, item prices, or exchange rates somewhere other than hardcoded JavaScript objects sitting in the front end. It wasn't a deep architectural dive, but it was the first real nudge toward thinking about persistence and server-side logic instead of everything living entirely in the browser.

## Lessons learned

A few things this project taught me that I still carry forward:

- **Small annoyances make great starting points.** You don't need a grand idea to start building — you just need something mildly irritating enough that solving it once feels worth the effort.
- **Constraints teach fundamentals.** Working without a framework forced me to actually understand JavaScript instead of leaning on abstractions I didn't fully grasp yet.
- **Curiosity compounds.** Adding a `server.js` "just to try it" planted the seed for everything I later learned about backends and data persistence — a seed I wouldn't have planted if I hadn't kept experimenting past the point where the project "worked well enough."
- **Nothing you build is really wasted**, even if the reason you built it disappears.

## Games fade — the skills don't

Dine Together, like a lot of NFT games from that era, has since faded. The hype cycle around blockchain gaming moved on, and the game's economy isn't what it once was. But here's the part that actually matters: none of the value I got out of building these tools disappeared with it. The JavaScript fundamentals, the instinct to automate an annoying manual task, the early curiosity about backends and databases — all of that stuck around and quietly shaped how I approach problems today, infrastructure work included.

That's the real lesson, I think. A "throwaway" side project for a game you're playing casually isn't throwaway at all. Even if the game itself doesn't last, the muscle you build solving real — if small — problems sticks with you long after the thing you built it for is gone.

## Still up, if you're curious

The calculator is still live at [dinetogether-calc.vercel.app](https://dinetogether-calc.vercel.app) — a small time capsule of a game that's mostly gone now, and proof that the best lessons sometimes come from the projects you never expected to matter.
