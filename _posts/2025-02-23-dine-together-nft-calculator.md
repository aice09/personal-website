---
layout: post
title: "Dine Together: What a Small NFT Game Calculator Taught Me"
date: 2025-02-23
tags: [javascript, webdev, personal]
---

Not every meaningful project starts with a grand plan. Some of the best ones start small, almost by accident — you're just trying to solve one annoying little problem, and you don't realize until much later how much it actually taught you.

Mine started because I was playing an NFT restaurant simulator game called Dine Together, built on the FactoryChain network, and got tired of doing math in my head every time I wanted to figure out tips, ingredient costs, or whether a piece of furniture was actually worth the price. So I built a calculator. Then another. Then a bunch more.

## From one calculator to a whole toolkit

Looking at the repo now, it's genuinely fun to see how far it grew from "let me just calculate my tips." There's a regular tip calculator, a bonus tip calculator, a combined tip-with-bonus calculator, a chef checker, a farming calculator (with a v2 after I clearly wasn't happy with the first version), a furniture info page, a dish calculator, marketplace pages for chefs and land, an exchange rate calculator, an energy calculator, and a synergy calculator. What began as a single utility page turned into a small suite of tools covering nearly every mechanic in the game.

That's one of the things I love about side projects like this — you rarely plan the scope up front. You just keep noticing "oh, I could calculate that too," and before long you've built something a lot bigger than you set out to make.

## Peeking behind the marketplace

Along the way, I spent a lot of time in the game's marketplace — buying and selling items, checking prices, trying to spot good deals. The more time I spent there, the more curious I got about what was actually happening behind the scenes, not just what the marketplace showed me.

So I did what a lot of self-taught devs eventually do — I popped open the browser's dev tools, went to the Network tab, and started watching what requests the site was actually making. That's how I first ran into GraphQL. I hadn't worked with it before, and seeing real query payloads go back and forth, instead of just reading about it in a tutorial, made it click in a way documentation alone never had.

That was the real turning point. Once I understood the marketplace was being driven by GraphQL queries pulling from a backend data source, I started thinking differently about my own project — not "what calculator should I build next," but "what if I built something that pulled from the marketplace data itself, backed by a real database instead of hardcoded numbers." That shift is what eventually pushed the project from a handful of static calculator pages toward something with an actual backend behind it — the reason a `server.js` and `package.json` quietly showed up in the repo.

## What building it all taught me

Most of the calculators are plain HTML and JavaScript — no framework, no build step, just straightforward DOM manipulation and math. That constraint turned out to be a gift. It's easy to reach for a framework by default, but building these reminded me how much you can accomplish with vanilla JS when the problem is well-scoped: read some inputs, run the formula, update the page. It also meant I got comfortable organizing a growing set of independent-but-related pages without everything turning into spaghetti — a skill that mattered a lot more later than I expected.

Looking back, a few things really stuck with me. Small annoyances make great starting points — you don't need a grand idea to start building, just something mildly irritating enough to be worth solving once. Constraints teach fundamentals — working without a framework forced me to actually understand JavaScript instead of leaning on abstractions I didn't fully grasp yet. And curiosity compounds — poking around someone else's marketplace requests is what led me to poke around my own backend, which is really where everything I later learned about GraphQL, APIs, and databases got its start.

## Games fade — the skills don't

Dine Together, like a lot of NFT games from that era, has since faded. The hype cycle around blockchain gaming moved on, and the game's economy isn't what it once was. But none of the value I got out of building these tools disappeared with it. The JavaScript fundamentals, the instinct to automate an annoying manual task, the early curiosity about GraphQL, APIs, and databases — all of that stuck around and quietly shaped how I approach problems today, infrastructure work included.

That's the real lesson. A "throwaway" side project for a game you're playing casually isn't throwaway at all. Even if the game itself doesn't last, the muscle you build solving real — if small — problems sticks with you long after the thing you built it for is gone.

## Still up, if you're curious

The calculator is still live at [dinetogether-calc.vercel.app](https://dinetogether-calc.vercel.app) — a small time capsule of a game that's mostly gone now, and proof that the best lessons sometimes come from the projects you never expected to matter.
