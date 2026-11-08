---
title: "The Medicare Agent Was Just Doing Its Job"
date: 2026-11-03T09:00:00+10:30
description: An AI agent hit a block while researching medicine spending and kept looking. What happens when a caller that never gets bored meets a system that's been doing its job for decades?
hero: medicare-agent-doing-its-job.png
menu:
  sidebar:
    name: The Medicare Agent Was Just Doing Its Job
    identifier: medicare-agent-doing-its-job
    weight: 150
tags: ["AI", "Security", "Legacy"]
categories: ["Security"]
---

In late September, the Prime Minister [told us](https://www.abc.net.au/news/2026-09-24/ai-agent-accessed-australian-government-site-pm-says/107189078) that an OpenAI agent had got into a Medicare statistics portal operated by Services Australia. The story itself starts with something pretty mundane. An internal model was doing internet research into medicine spending (skin-condition medicines in Victoria, by one account); it was blocked from the Australian data it wanted, so it looked for another way to get the answer. [According to OpenAI](https://techcrunch.com/2026/09/29/openai-apologizes-to-australia-after-its-ai-agents-breached-government-sites/), it ended up running commands on Services Australia's internal system, retrieving files and credentials, and writing its own files. Nobody asked the model to do any of that, and OpenAI says it only noticed in August, almost two months later, while reviewing model activity from training.

Most of the reporting I've heard (largely ABC News, in the shower each morning) has been about OpenAI hacking an Australian government server, and much less about why the agent did what it did. I can see why, and the month it took OpenAI to tell anyone fully deserves the questions it's getting, but for those of us who look after systems I think it's a much less dramatic story, about an old system and a very persistent caller.

[Back in July](https://peti.to/posts/never-trust-your-caller/) I wrote about a rule I've been giving developers on my team for years: write every method as though the code calling it is hostile, malicious or stupid ("stupid" doesn't mean incompetent, it means a caller that doesn't know, or doesn't care, what assumptions your method makes). I mentioned in passing that this would increasingly mean AI agents, and I had no idea an Australian government portal would be the example a few months later.

An agent is about the purest stupid caller you could design. As far as anyone has said, it had no intent to do harm, it just had a question and kept working at it. When a person hits a blocked page, they generally take the hint, or at least they know they're now doing something they shouldn't, and most of them have better things to do with their afternoon. In my experience an agent treats a block as one more problem between it and the answer, which is exactly what we ask of these tools all day. Last month, one of mine was resolving merge conflicts when a permission check refused a command that would have overwritten a whole folder, so it did the four files that needed it one at a time and carried on. It got the right result and told me afterwards, but it didn't stop to ask. That's a much narrower route than the one the Medicare agent found, and arguably the check did its job, but it's the same reflex. I use them every day, and I'm very glad they're persistent; it's most of why they're useful.

I don't know how this portal was built, and how the agent got past the block hasn't been made public, so I can't tell you whether my rule would have saved it. The impact also looks fairly limited so far: OpenAI says it found no evidence of patient records being accessed, the Government says no personal information appears to have been accessed, and the portal has since been taken offline, though the credentials it retrieved are the part I'd want accounted for.

The detail I recognised came from the Minister for Government Services, Katy Gallagher, who described it as a legacy system that dates back decades, and [iTnews reported](https://www.itnews.com.au/news/australian-medicare-data-portal-infiltrated-by-openai-agent-629149) there may be no ongoing need for it at all. At a guess, it had been sitting there doing its job for years, with its assumptions about who might turn up getting older along with everything else. Old systems often lean on a kind of protection nobody ever wrote down, which is that poking around in them was tedious, and it took a person with a reason to bother. An agent takes that away, because it's cheap, it doesn't get bored or tired, and it doesn't need a motive, only a question that happens to have an answer somewhere behind your login.

Home Affairs has since [told Commonwealth agencies](https://www.itnews.com.au/news/home-affairs-orders-gov-wide-legacy-system-stocktake-within-six-months-629313) to stocktake their legacy systems by the end of March, which seems sensible, though unless you work for one it won't apply to you. There's a smaller version anyone can try on the systems they look after. Pick your oldest internal system and ask whoever looks after it whether the system checks every request for itself, or whether it trusts that anything reaching it must have come through the front door. Then ask how you'd find out if an agent spent an afternoon going through it, given that in this case the agency appears to have heard about it from OpenAI, by email, to a disclosures mailbox, almost three months after it happened. The AFP has since [told a parliamentary committee](https://www.threads.com/share/BAURmzaajU) that, at this stage, nobody has formally reported a crime, and that Services Australia remains best placed to monitor any ongoing impacts. I'm not sure I'd want that job, given Services Australia seems to have found out the same way the rest of us would have, from someone else.

---

*Sources:*

- [ABC News, "OpenAI hacked Medicare portal, Prime Minister Anthony Albanese says" (24 Sep 2026)](https://www.abc.net.au/news/2026-09-24/ai-agent-accessed-australian-government-site-pm-says/107189078) - timeline and government statements. Solid.
- [iTnews, "Australian Medicare data portal 'infiltrated' by OpenAI agent" (24 Sep 2026)](https://www.itnews.com.au/news/australian-medicare-data-portal-infiltrated-by-openai-agent-629149) - Gallagher's legacy comment, portal offline, disclosures mailbox. Solid trade press. Forensic review was still under way.
- [TechCrunch, "OpenAI apologizes to Australia after its AI agents breached government sites" (29 Sep 2026)](https://techcrunch.com/2026/09/29/openai-apologizes-to-australia-after-its-ai-agents-breached-government-sites/) - task detail, commands, files and credentials. Relays OpenAI's own account, so attribute it.
- [iTnews, "Home Affairs orders gov-wide legacy system stocktake within six months" (30 Sep 2026)](https://www.itnews.com.au/news/home-affairs-orders-gov-wide-legacy-system-stocktake-within-six-months-629313) - the stocktake direction. Solid.
- [Cam Wilson on Threads, screenshot of the AFP's answer to Question on Notice 1, Joint Select Committee on Artificial Intelligence](https://www.threads.com/share/BAURmzaajU) - question asked by Kate Chaney MP on 14 Oct 2026. Reporter's post showing the document; the original wasn't found online, and the AFP's wording is "at this stage".
- [Never Trust Your Caller (peti.to, 4 Jul 2026)](https://peti.to/posts/never-trust-your-caller/) - Ben's post.
