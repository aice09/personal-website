---
layout: post
title: "Talking to a Biometric Device in PHP: My ZKTeco Integration"
date: 2026-07-11
tags: [php, mysql, webdev]
---

Some of the most interesting integration work isn't with a clean, modern REST API — it's with a piece of physical hardware that just wants to talk UDP and doesn't care how modern your stack is. That was the case with getting a ZKTeco biometric time-and-attendance device to actually talk to PHP.

## The device, not just the software

ZKTeco devices — the fingerprint and biometric attendance terminals you'll see mounted at office entrances — don't expose a friendly HTTP API. They speak their own protocol over raw UDP, specifically on port 4370, following a communication spec that's really meant for embedded device firmware, not typical web development.

To bridge that gap, I built on top of `php_zklib`, an existing PHP library that already knew how to speak the device's language — opening a UDP socket, sending the right command packets, and parsing whatever came back into something PHP could actually use.

## From raw protocol to real data

Getting the connection working was really just step one. The actual goal was to pull attendance logs — check-in and check-out timestamps — off the device and get them into a database where they could be queried and reported on, instead of living only inside the device's own limited internal storage.

That meant writing the pieces that turned "raw device output" into something useful:

- A script to connect to the device and pull attendance records
- A process to take that data and insert it directly into a database, so records were preserved and centrally queryable
- A sync routine to keep the process repeatable — not a one-off pull, but something that could run regularly to keep the database current with whatever the device had recorded

## What made this fun

There's something satisfying about integration work at this level. You're not just calling a REST endpoint and parsing JSON — you're working with a real protocol spec, understanding command structures meant for embedded hardware, and translating that into something a web application can actually use. It's a reminder that not everything in the world speaks HTTP, and being comfortable dropping down a layer — to sockets, to binary protocols, to hardware communication — is a genuinely useful skill to have.

It also reinforced something I keep re-learning in different contexts: a lot of real infrastructure and systems work is glue work. The device already existed. The PHP library already existed. My job was building the bridge between them — getting attendance data out of a closed device and into a system where it could actually be used, reported on, and trusted.

## Why it stuck with me

This wasn't glamorous, headline-feature work. It was quiet, practical integration — but it's exactly the kind of project that teaches you to be comfortable with unfamiliar protocols and legacy hardware instead of only ever working with clean, modern APIs. And that comfort has paid off more than once since, whenever I've run into some other system that insists on doing things its own old-fashioned way.
