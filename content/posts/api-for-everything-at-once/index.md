---
title: "What Happens When Someone Asks Your API for Everything at Once?"
date: 2026-08-16T09:00:00+09:30
description: A missing page-size cap let a built-in REST endpoint load an entire table into memory in one response. The fix was neither difficult nor clever, but landing it once in the shared framework meant every client inherited it on their next upgrade.
hero: api-for-everything-at-once.png
menu:
  sidebar:
    name: What Happens When Someone Asks for Everything at Once?
    identifier: api-for-everything-at-once
    weight: 130
tags: ["Security", "API", "Enterprise"]
categories: ["Security"]
---

A client had been bulk-loading data straight into the database with SQL, to speed up onboarding new customers. It's a reasonable instinct, SQL is fast and easily repeatable with minimal effort, but writing directly to the database skips the permission checks, validation and auditing the application does for you. So I suggested our platform's built-in REST API instead, and built them a Postman collection to try it. Somewhere in the testing they sent off a couple of requests with no page size on them, and the development server ran out of memory and fell over. Then it did it again.

It took me a bit to work out why a single request could take the whole server down. Turns out, we basically left a built-in denial of service. One of the built-in list endpoints, the sort of thing that hands a page of records back to a screen or an API caller, had no enforced maximum page size. Ask it for a page and tell it how big, and it behaves. Leave the page size off, or set it to a value the underlying query reads as "no limit" (easier to do by accident than it sounds, especially mid-test), and it doesn't fall back to a sensible default. It goes and fetches the entire table.

Then it does the expensive part. It populates every one of those records in full and serialises the lot to JSON in a single response. If there is only a few hundred rows, you'd never notice. A big table, and the memory to hold all of it at once is enough to exhaust the heap and bring the process down. Nobody had to be malicious, or even clever. A caller just had to forget to ask for a page.

This has a name, as it happens. It sits fourth on the [OWASP API security list, "unrestricted resource consumption"](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/), and the textbook fix is exactly what you'd guess: enforce a maximum page size, and refuse the query that quietly asks for everything. I capped it at a hundred rows and rejected the aggregate queries that could pull the same stunt a slightly different way. Not a difficult or clever fix, just one that should have been there from the start. A page cap only handles the single greedy request, mind - it won't stop someone calling the endpoint a thousand times over, which is really a rate-limiting job. I've put that on the list to add rather than pretending I solved it this week.

In fairness to the endpoint, it's opt-in - it only applies to applications that turned on Skyve's built-in REST layer, and this client happened to be the first to really lean on it. So this was a latent thing waiting to surface with real use, rather than a door that had been standing open. Absolutely still worth fixing regardless, but not the sort of exposure anyone needed to lose sleep over.

I'd patched it in that client's copy, which solved their immediate problem. But the endpoint isn't really theirs. It's part of the framework every one of our clients runs on - our own platform, Skyve, which is open source, so [the change is public if anyone wants to read it](https://github.com/skyvers/skyve/pull/326). So I made the same fix once more, in the framework itself. That doesn't reach into every running system on its own, each client's app picks it up the next time it's upgraded to the latest framework version, but the work is done once, and every other client inherits the fix on their next upgrade, including all the ones the bug had never actually bothered.

That's a kind of advantage that never shows up on a feature comparison. When an organisation runs a sprawl of one-off systems, a bug like this gets found and fixed one system at a time, assuming anyone thinks to look for it in the others at all. When those systems sit on a shared foundation, a defect surfaced by one of them can be fixed for all of them in a single change, and the rest pick it up as they upgrade.

So the plain question is worth putting to your own team, because nearly everyone owns APIs or internal tools now: what happens if someone requests all of it at once? On a small table, nothing - you'll never notice. On a big one, the honest answer is sometimes "the server falls over", and it is a good deal nicer to learn that from a colleague testing a screen or an endpoint (or a MCP server) than from production on a Friday evening.
