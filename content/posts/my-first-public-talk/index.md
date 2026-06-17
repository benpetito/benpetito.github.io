---
title: "My First Public Talk — What Actually Stuck With Me"
date: 2026-06-12T09:00:00+10:30
description: I gave my first public talk at the Adelaide Azure User Group. Then I spent four days proving my own point.
hero: my-first-talk.png
menu:
  sidebar:
    name: My First Public Talk
    identifier: my-first-public-talk
    weight: 20
tags: ["AI", "Speaking", "Enterprise"]
categories: ["General"]
---

I gave my first public talk a few weeks ago. Then I spent four days proving my own point — not on the AI, on the slides.

It was at the Adelaide Azure User Group. Thanks to Simon Cook from Encode Talent for the introduction and nudge to speak, and to Sam Fernando for running it. I dragged my coworker Simeon from Biz Hub along for the ride.

The talk was about practical AI implementation in enterprise environments. The central theme was simple: the AI model is usually the easy part. Everything around it is where the real work happens.

I'd written the abstract back in February, then did what I always do — procrastinated productively. Bullet points went into a phone note at the bus stop, in the shower, half-listening to a podcast. Three or four weeks out I had a skeleton. Two jobs left: build the deck, and learn the thing.

The deck is where it fell apart. I'd tried to get Copilot to build slides for some customer training back in February. It flat out refused to edit an existing presentation — every change, a brand new file to download. I assumed Microsoft would have fixed that by now. It had not. Then a dad at school mentioned Claude could do PowerPoints. I fed it my notes and it produced a genuinely good-looking deck in one shot — until I hit the usage limits and got locked out for hours after a prompt or two. What actually solved it was a mate who asked why I was using PowerPoint at all, and handed me a skill that generates slides as HTML. Clean file, edit it myself, done.

I'd estimated half a day for all of this. It took four days and about nine hours of real work, mental health breaks included.

The part that stuck with me: I was nervous and self-conscious reading from my notes. The talk only came alive at the end, in the Q&A, when I could put the cards down, look at people, and answer from experience.

Someone asked when we switch models partway through a prompt chain. We don't, yet — but we do give the same model different personas depending on the job. The real rule is duller than people expect: use the simplest, cheapest model that does the task reliably, and only reach for more when it earns its place. Another asked whether running local models is worth it. My answer: unless privacy is paramount, Azure's data sovereignty guarantees are good enough for government work, and the infrastructure and maintenance aren't worth the cost or headache. Paying for it as a service is much cheaper, and you get a better model — for now.

Those are the questions I can answer without notes, because I've had to live with the answers.

The feedback that meant the most: they'd sat through AI demos before, but this was the first one about actually putting it into production. The technology is the easy part to talk about. The reality of running it is where the conversations that matter start.

So yes: the model was the easy part. Everything around it was the real work. Including, apparently, talking about it.
