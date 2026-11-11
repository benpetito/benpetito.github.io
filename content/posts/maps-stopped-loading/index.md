---
title: "The Day Our Maps Stopped Loading"
date: 2026-11-11T09:00:00+10:30
description: Using something free doesn't remove the ownership cost. When OpenStreetMap started blocking our map requests, a small fix had to find its way through more than ten applications.
hero: maps-stopped-loading.png
menu:
  sidebar:
    name: The Day Our Maps Stopped Loading
    identifier: maps-stopped-loading
    weight: 160
tags: ["Integrations", "Architecture", "Enterprise"]
categories: ["Engineering"]
---

Back in September, the maps stopped loading in several of our apps. Where the map should have been, we were getting tiles saying access was blocked. A client testing a new release in their test environment picked it up and reported it to me, though it turned out production users were seeing it too. As far as we could see, nothing on our side had changed, which is usually the moment you start checking everything on your side anyway.

Those apps got their maps from [OpenStreetMap](https://www.openstreetmap.org), a free community-run service that's excellent at what it does. Its tile policy (a tile being one of the small square images a web map is stitched together from) has asked for years for browsers to say which site a request came from, and this year it started blocking the ones that don't. That information travels in the Referer header (the [misspelling](https://en.wikipedia.org/wiki/HTTP_referer#Etymology) has been in the standard since the 1990s, and we've all just agreed to live with it). We'd shown OSM's required attribution on our maps since we first added it, but our apps were deliberately not sending the Referer. Our platform, Skyve, keeps its Referrer-Policy tight as a privacy default, so that your site's addresses don't leak to every third party the page talks to. It's a good default, and it was also exactly why OSM saw no Referer and blocked us.

I can't really complain about them wanting to change it. OpenStreetMap's [tile policy](https://operations.osmfoundation.org/policies/tiles/) says plainly that availability is best-effort with no SLA, that the policy may change at any time, and that access may be blocked without notice. They're giving away something expensive to run, and they're entitled to set the terms. We weren't the only ones caught either: Simon Willison [hit the same referrer block](https://simonwillison.net/2026/May/5/datasette-referrer-policy/) on Datasette back in May.

There were a couple of different options to make it work; none of them was particularly hard, but I was pretty happy with the one we ended up going with. The easiest option was to loosen the policy across the whole app, but I left it at `same-origin` everywhere and relaxed it for the map layer only to `strict-origin-when-cross-origin`, which sends the site's address and nothing about the page. So the map provider gets the minimum it asked for, and nothing changes for any other request the page makes. Then it had to go into the framework and be pulled through each application in turn (more than ten of them actively use maps), which, of course, made this seemingly small fix turn into a half-day excursion. Depending on how far behind on Skyve versions an app was, that meant a redeploy or just a config change, and a couple of them still have it come back every so often; I ran into one at a client on Friday.

When I wrote about [build vs buy](https://peti.to/posts/build-vs-buy-bill-moved/) in July, I was mostly talking about the cost that starts the day after you ship something you built. This felt like the same idea from the other side, using something free doesn't remove the ownership cost, but it does mostly mean you don't get a say in when it arrives. That's often a perfectly good deal, and I'm certainly not suggesting anyone run their own map tile servers; for commodity plumbing like this, using someone else's service is the right call almost every time.

Our platform has always allowed a paid tile provider, Google Maps, to be switched on if someone wants to pay for it. But I've always insisted on a free option for our smaller clients to use right away, without setting up a billing account. Switching wouldn't have helped much anyway: without an API key linked to an active billing account, Google puts a watermark over its maps, so it's just a different tile loading problem, and asking every client to turn on billing was more of a headache than the OSM fix. I ran into an example of this on my doctor's website on Monday when trying to book an appointment.

![A Google map greyed out behind a "This page can't load Google Maps correctly" dialog, with "For development purposes only" stamped across the map](images/2026-11-03-google-maps-dev-watermark.webp)

The free things tend to arrive one at a time, added by a developer on a busy afternoon for good reasons, and nobody goes back and counts them. It's worth knowing which ones you've embedded across how many systems, and what your users would see if one of them went away tomorrow (obligatory [xkcd reference](https://xkcd.com/2347/)). Going forward, supporting more alternative tile providers is probably the best solution, but there are only so many hours in the day. I'll get to it eventually…