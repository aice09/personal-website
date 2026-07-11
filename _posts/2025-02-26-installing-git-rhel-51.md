---
layout: post
title: "Installing Git on an Old RHEL 5.1 System"
date: 2025-02-26
tags: [linux, git, rhel, devops]
---

Every so often, legacy infrastructure hands you a puzzle that modern tooling has completely forgotten how to solve. I ran into exactly that while working at Microchip Semiconductor, poking around some legacy Linux systems that had been quietly running in the background for years. Tucked in there was a RHEL 5.1 box — old enough that half the modern package repos wouldn't even acknowledge its existence anymore.

Naturally, I needed Git on it. And naturally, `yum install git` was never going to be the answer.

RHEL 5.1 predates any package repo that ships a usable Git version, so getting it running meant going back to basics — pulling source, compiling it by hand, and working around a system that was never designed with today's tooling in mind. Here are two ways I got it done.

## Option 1: Manual Installation via Tarball

If you want full control over the installation process, this is the way to go.

**Steps:**

1. Download the tarball on your local Windows PC from the [Git tarball mirror](https://mirrors.edge.kernel.org/pub/software/scm/git/)
2. Grab **git-2.9.5.tar.gz** from that link
3. Transfer the file to your RHEL box using an FTP tool like WinSCP or FileZilla — no direct internet access on that legacy box meant this step wasn't optional
4. Run the following on the RHEL system:

```bash
tar -xvzf git-2.9.5.tar.gz  # Extract the tarball
cd git-2.9.5                # Navigate into the extracted folder
make prefix=/usr/local all  # Compile Git
make prefix=/usr/local install  # Install Git
git --version               # Verify installation
```

Watching a decade-old OS successfully compile a modern-ish Git version from source was oddly satisfying — proof that even ancient systems can still be taught a few new tricks.

## Option 2: One-Line Command Installation

If you want a faster, no-nonsense approach — and the box actually has outbound internet access, which not all legacy machines do — run this as root:

```bash
cd /tmp && wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.9.5.tar.gz && tar -xvzf git-2.9.5.tar.gz && cd git-2.9.5 && make prefix=/usr/local all && sudo make prefix=/usr/local install && git --version
```

This single command downloads, extracts, compiles, and installs Git in one shot — a nice shortcut when you don't want to babysit four separate steps.

## Conclusion

Installing Git on RHEL 5.1 isn't straightforward, but it's absolutely doable — and honestly, there's something fun about keeping a legacy system alive and useful instead of just writing it off as too old to bother with. Pick whichever method fits your situation:

- Manual installation — for full control, especially on air-gapped or restricted boxes
- One-liner command — for a quick setup when the box has internet access

Legacy systems have a way of showing up right when you least expect them. Knowing how to work with them, instead of around them, is half the job.
