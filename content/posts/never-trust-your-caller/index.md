---
title: "Never Trust Your Caller"
date: 2026-07-04T09:00:00+10:30
description: The FIFA World Cup hack wasn't really about client-side trust. It was about a backend that assumed every caller had already been checked.
hero: never-trust-your-caller.png
menu:
  sidebar:
    name: Never Trust Your Caller
    identifier: never-trust-your-caller
    weight: 60
tags: ["Security", "Architecture", "Enterprise"]
categories: ["Engineering"]
---

A couple of weeks ago a security researcher published an excellent write-up of how they could have hijacked every live camera feed of the FIFA World Cup. There was no clever exploit involved. They registered as a football agent on FIFA's public portal, which quietly added their account to the same Microsoft Entra tenant that runs FIFA's internal platforms. When they browsed to the internal streaming system, the front end correctly showed them an access denied page. The backend, on the other hand, served everything they asked for - including the ingest URLs and stream keys for every camera at every match. Their own summary of the impact: they could have rickrolled the World Cup final.

The security commentary has mostly landed on the obvious lesson, "never trust client-side security", and that's been a rule since the dawn of the web. But I think it undersells what happened. The client-side checks weren't really the problem. The problem was the assumption sitting behind the backend: if a request made it this far, the caller must be allowed to be here.

I've been telling developers on my team some version of the same rule for years: write every method as though the code calling it is hostile, malicious or stupid ("stupid" doesn't mean incompetent, it means a caller that doesn't know, or doesn't care, what assumptions your method makes). It sounds paranoid when you say it out loud, but very little of it is about attackers. In my experience, most unexpected input doesn't come from someone hostile at all. It comes from another developer who misunderstood your API, an integration that sends a field you didn't expect, an old code example copied from a wiki long after it stopped being accurate, or future me, six months from now, having forgotten what I originally intended (I seem to run into that last one more than all the others combined). And increasingly it will come from AI agents, which will happily call your endpoints in combinations no human ever thought to try.

The "stupid" caller is the one worth designing for, because they're the common case. A method that validates its own inputs, checks its own permissions and fails loudly when something is off will handle the hostile caller almost as a side effect. FIFA's backend had it the other way around - it relied entirely on its callers arriving well-behaved, and the one time a caller wasn't, there was nothing left to stop them.

The part of the write-up that would have made me pull my hair out (if I had any left) wasn't even gaining access to the streaming panel. It was that the researcher found system after system - the data platform, the commentator dashboard, a stray dev environment - all trusting the same identity provider, and all assuming that anyone past the front door belonged there. I see that same shape in enterprise software regularly. Organisations spend weeks (or months) getting authentication right, then treat authorisation as an afterthought. But logging in only answers "who are you?". It says nothing about "what are you allowed to do?", and those are completely different questions. FIFA answered the first one properly. Nobody was answering the second on the server, which is the only place the answer counts. Your systems are checking roles on the server, right? And I hope those roles are fine-grained.

There's a second lesson in there too. At a guess, those FIFA systems were built as internal tools, for a small set of trusted staff, and the trust assumptions probably made sense on day one. Then a public registration portal got wired into the same tenant, and every one of those assumptions silently expired. Today's internal API has a habit of becoming tomorrow's public endpoint, usually without anyone going back to check what it was built to assume.

So I don't write code expecting everyone to use it correctly. I write it expecting someone, eventually, to misuse it by accident - another developer, an integration, an AI agent with a creative interpretation of my API. Good software isn't just correct on the happy path. It's hard to use incorrectly.

---

*Sources:*

- [BobDaHacker, "I Could've Rickrolled the Entire FIFA World Cup. All I Needed Was My ID." (16 June 2026)](https://bobdahacker.com/blog/fifa-hack) (researcher's first-person disclosure - single source, FIFA never responded publicly, so the details are their account)
