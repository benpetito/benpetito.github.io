---
title: "AI Never Tells You It's Done"
date: 2026-08-24T09:00:00+09:30
description: AI coding agents are more capable than ever, and they will always write more code, they just won't tell you when to stop. The code has gotten cheap. Understanding it, which is what ownership actually is, never has.
hero: ai-never-tells-you-its-done.png
menu:
  sidebar:
    name: AI Never Tells You It's Done
    identifier: ai-never-tells-you-its-done
    weight: 140
tags: ["AI", "Legacy", "Enterprise"]
categories: ["General"]
---

I have been having ongoing conversations with our chief architect, Mike, over the past couple of years as we've watched AI become progressively more "intelligent" and competent in software development. Mike played the role of a curmudgeon, as he is wont to do, and was extremely sceptical that it would ever surpass human engineers. In the meantime, I had been reading, tinkering, and watching the steady, inevitable progression of new models.

Their usefulness seemed quaint at first, and it took a lot of effort and scaffolding to get anything consistently useful from the early models. I found it was good at adding tooltips and documentation around the place, but the code it wrote was, to be honest, a hot mess. Point to Mike.

I can't remember exactly when it was, but around the middle of last year I definitely noticed a point at which the models were better than a junior developer, and I discussed with Mike what the impact would be on new developers (or the lack of them) coming out of university. Right at the end of last year we saw another jump in capabilities, and I stopped looking at the code as it was being written and just checked it at the end. I was now managing a team of agents who were faster and more capable, and who took less time to explain what I needed than it did to involve a junior developer.

Fast-forward to the present: the whole team (including Mike) now uses AI agents at almost every stage of the lifecycle. You can try to resist and take a purist stance, loving the craft of writing code, but you will get left behind; all our competitors are using it.

The models are more than capable, and in some respects almost too capable. AI will always write more code - it will never tell you it's done. At Biz Hub, we use a low-code platform to deliver bespoke solutions for our customers. As the name implies, this should have less code than a typical software application, somewhere in the ballpark of 60-70% less. Yet, with the assistance of an AI coding agent, we've ended up with a proliferation of extra files and supporting tests that no one has internal ownership of, and that can't be maintained without an AI.

AI has made writing code cheap; understanding it has never become cheaper, and understanding is what ownership is. My conversations with Mike now focus on how we can stop the AI from polluting our codebases with detritus, maintain quality by always having a human-review gate, and ensure there is some understanding of the new functionality.

Our company specialises in [modernising legacy applications](https://www.bizhub.com.au/legacy-conversion/). Many of those that come across our desk suffer from a lack of ownership, an Access database whose sole author has now retired, or an unsupported technology that no longer has a licence to run. If the entire world is now prompting its way forward at breakneck speed, who has a chance of understanding and maintaining this mess, other than, hopefully, a smarter AI with a bigger context window?

What happens to this if the bottom falls out of the venture-capital-backed American foundation models? If the cost of AI skyrockets and the easy access everyone has had is no longer sustainable, how will everyone cope with this insane lack of ownership, and what will immediately become technical debt? We already personally experienced roughly a [15x increase](https://peti.to/posts/ai-bill-usage-based-billing/) in prices this June.

We can no longer put the genie back in the bottle, but one of the amazing things LLMs brought to the world was their ability to level the playing field. They made this accessible to everyone at a reasonable cost. Is this going to be yet another thing gated for the massive corporations that can afford it?

The obvious rebuttal is that AI will maintain it too, and costs will keep falling, so ownership doesn't matter. That's possible, and I'm hopeful. Chinese open-source models, despite lacking access to American GPUs, are doing truly innovative things and are only a [couple of months](https://newsletter.semianalysis.com/p/are-open-models-catching-up) behind the foundation models in capability. And do we need trillion-parameter models that contain the sum total of the world's knowledge to make changes to a software project? There are open-source models I can (almost) run on my laptop that cover most of my use cases. So maybe the future is highly focused, specific small language models that don't require an entire town's [water supply](https://www.abc.net.au/news/2025-12-10/demand-for-data-centre-water-in-ai-push/106102208) to cool a data centre.

So, are we now using these tools to generate legacy, unsupportable applications? I think we might be, and faster than we ever used to inherit them. We're producing code nobody owns while we stop training the junior developers who used to grow into the people who would own it, and we're trusting that the AI that wrote the mess will still be here, and still cheap enough, to explain it back to us when it breaks. Maybe smaller, more focused models keep that a safe bet, and maybe the developers starting out native to all this find uses the rest of us can't see. But it is a huge bet, and I would rather we went in with our eyes open and accepted the risk.
