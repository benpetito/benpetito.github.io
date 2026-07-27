---
title: "The Tyranny of the Default"
date: 2026-07-27T09:00:00+09:30
description: A Checkstyle gate had been green for 833 builds because we never wired it up. A green tick only tells you a step returned success, not that it ran, or that it was checking anything.
hero: the-tyranny-of-the-default.png
menu:
  sidebar:
    name: The Tyranny of the Default
    identifier: the-tyranny-of-the-default
    weight: 110
tags: ["DevOps", "CI/CD", "Enterprise"]
categories: ["Engineering"]
---

This term was coined by Steve Gibson on his [Security Now](https://www.grc.com/securitynow.htm) podcast many years ago. Steve realised that most people will never touch or change the default settings/options that come with any software. As part of delivering custom software solutions to our customers, this is something I think about fairly often, as the choices we make, while we give our customers levers to pull, rarely get changed.

I tripped over this last week in a slightly different situation while investigating for a customer what static analysis and CVE detection was in place for their application. We use GitHub for almost all of our projects and have a mature, well-worn CI pipeline (the thing that builds the application and runs checks and tests when new code is pushed). But for this customer, we host it in their Bitbucket repository.

When we began this project in 2024, we set up our Bitbucket pipeline using a template (writing these things from scratch is a pain; they all come with templates). While documenting all the dependency scanning, container image scanning, and severity thresholds that we were doing correctly, I came across a Checkstyle check that we thought had been running the whole time. While this doesn't have anything to do with the security of the application code (Checkstyle enforces coding-standard conformance), it was a bit of a shock that this had been on since build #1 but didn't actually do anything because we forgot to wire it up on the application side!

If the check had been flagging conformance issues the whole time, someone would have gone looking. But because it was silently passing, 833 builds later it was still doing nothing. A missing check is a risk that you know about. A green one that is silently passing is a risk you've been told doesn't exist.

My week of pipeline admin work aside, many of us govern software by dashboard these days, chasing rows of green lights. But green only tells you a step returned success; it doesn't tell you whether the step ran, whether it ran against this change, or whether it was set up to check anything worth checking. "All our gates are green" makes you feel good when you trigger the production deploy, or is the line you give to a nervous board, and it's often true in precisely the way my Checkstyle tick was true.

The slightly uncomfortable irony is that I only found this because we were adding more gates, not fewer. The more checks you stack up, the more places there are for one of them to stop pulling its weight without telling anyone. While I was at it, I found a cousin of the same problem on a new project in the same week. A scanner switched on but not yet tuned, dutifully reviewing and blowing our line count on generated code that the framework had already validated. Not a dead gate this time, just a loud one saying nothing useful. The same issue, just a slightly different flavour.

So if you run software and take comfort in a wall of green ticks, a small habit worth building is to occasionally check that those checks are earning their place. Did it actually run against this build, and is it configured to catch anything you'd care about? When did the check last go red? A gate that has never once failed in the life of a project has a good chance of either not running or not being set to catch anything that happens in your code. These aren't exciting things to review; the entire appeal of a green tick is that it lets you stop thinking about it. Mine had been green and idle since the day the project was born, and the only reason I noticed was that I happened to be looking right at it.
