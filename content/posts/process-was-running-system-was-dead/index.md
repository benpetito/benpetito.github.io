---
title: "The process was running. The system was dead."
date: 2026-07-20T09:00:00+09:30
description: A container had JAVA_OPT instead of JAVA_OPTS. Every health check stayed green while the system quietly stopped doing its job for 42 hours.
hero: process-was-running-system-was-dead.png
menu:
  sidebar:
    name: The process was running. The system was dead.
    identifier: process-was-running-system-was-dead
    weight: 90
tags: ["Observability", "Java", "Enterprise"]
categories: ["Engineering"]
---

A couple of weeks ago, one of the systems we built and support stopped doing its job for about 42 hours, and almost nothing looked wrong while it happened.

It's a document-processing system: documents arrive by email; an AI step reads the PDFs and turns them into structured data; our application validates and processes them; and the results flow out to the customer's system of record. They'd not long gone live and were steadily onboarding new clients, so the volume was climbing week on week. Over a weekend, the scheduled processing simply stopped. The web application stayed up; the Java process never fell over; and every check watching it kept reporting the service as healthy. No crash, no alert. It only surfaced because someone noticed documents weren't coming through, not because anything told us.

That distinction turned out to matter more than anything else. Every monitor was checking the first claim (the process is running), and none was checking the second (the system is working).

There was nothing to work backwards from, no crash and no stack trace, so I had to reconstruct the weekend by piecing together evidence from a few different places: the server logs, Azure's activity logs, the container's revision history, the JVM's startup arguments, and the database metrics. Once it was all assembled, the logs told a consistent story - over a hundred out-of-memory errors across Friday and Saturday, the database connection pool exhausting itself as a knock-on effect, scheduled jobs failing, and a few restarts along the way.

The root cause was almost embarrassingly small. The container had an environment variable set to `JAVA_OPT` instead of `JAVA_OPTS`. Our application server only reads `JAVA_OPTS`, so it silently ignored the memory settings we thought we'd handed it and fell back to its own default - a 512 MB heap instead of the roughly 2.5 GB it was meant to have, about a fifth. Every restart in the logs confirmed it, quietly starting up with its default 512 MB. One missing character, no error, no warning.

This had been wrong since around March, and it had run perfectly fine for months. The configuration never changed, but the workload did. Pulling the document volumes out of the database, I could see processing had grown roughly sevenfold between April and June as the customer onboarded new clients into the platform. The undersized heap was fine at low volume and became inadequate as throughput climbed, which is why a March misconfiguration didn't bite until July. Nobody made a change that broke the system - the system simply caught up with a fault that had been sitting there the whole time. "Nothing changed" is one of the least reliable sentences in this job. Something changed; it just wasn't in the config.

The reason it ran for 42 hours, not 42 minutes, came down to one detail of how Java behaves. During a memory-exhaustion event on Saturday afternoon, an out-of-memory error interrupted the one-time setup of a core validation class. In Java, once a class fails that initialisation, every later attempt to use it throws an error for the entire life of that process - it never recovers on its own. From that moment, document validation was broken for good, but because nothing restarted afterwards, the same poisoned process kept running right through into Monday. The process was up. The thing the process existed to do was dead.

We got lucky in one respect. The failure happened at the validation step, before anything had been accepted into the system, so the affected emails were held upstream rather than swallowed and lost. Once we'd fixed it, they were re-sent and processed normally. Where a failure sits in the pipeline decides whether it's a data-loss event or just a delay, and fortunately, this one happened to fail on the safe side of that line. I'd like to claim that was all design. Some of it was; some of it was a bit of luck.

This was exactly the sort of thing we expected our monitoring to catch. The container was set up to be monitored by Datadog, and the errors were all there - the application was writing every one of those hundred-odd out-of-memory events into the log file we ship to Datadog. They just never made the trip. The upload had been broken for over a month, an unrelated problem nobody had noticed, so the errors piled up in a file on the box, and no alert ever fired. You can't alert on data that never arrives. The availability checks, meanwhile, were working perfectly - and they were only ever asking whether the service was up, which it was.

There's a mild irony in it, because Datadog put out a report this year making more or less this exact point. Their argument, in their State of AI Engineering work, is that the thing now sinking systems in production isn't the cleverness of the model, it's operational complexity - and that a large share of production failures are silent, no error and no timeout, just a system that has stopped doing the useful thing while looking fine. (I've seen the silent share quoted at around three-quarters; I'd take the precise number with a grain of salt, but the shape of it matches what we lived.) We had the tool, and the signal was even sitting there in the logs - it just never made it to Datadog, and we weren't asking it to watch the one thing that mattered anyway.

The fix itself took minutes - we corrected the variable to `JAVA_OPTS` and restarted. I checked the running process directly afterwards, rather than trusting that the change had taken, and confirmed it was finally running with the heap it should have had all along. The out-of-memory errors stopped, processing returned to a normal volume, and memory usage settled at about 17%, down from near 62% just before the fix.

Typos are unavoidable, and a decent system should be able to survive one. What this incident was really about is a gap most monitoring leaves wide open: "the process is running" and "the system is working" are different claims, and it usually only checks the first. A health check that confirms something is alive but not that it's getting through its work will happily sit there green while nothing happens. If you're only going to watch one thing, watch the work getting done, not the process staying up.

The two changes that would have caught this are both unglamorous. One is a health signal that inspects the real state - is the memory what we expect, are the scheduled jobs actually running, rather than just whether the process answers the phone. The other is managing the container's configuration as version-controlled infrastructure rather than a value typed into a portal, which wouldn't only have caught the typo in review - it would have told us exactly when the bad value went in, which the portal history couldn't.

That second point landed for me last week. I was at the [Adelaide Azure User Group](https://www.meetup.com/adelaide-azure-user-group/events/315489630/?eventOrigin=group_past_events), and as part of secrets permission management in Azure, David Gardiner was talking about managing infrastructure as code and version-controlling the changes that move between environments. We should never have set that environment variable by hand when we stood production up. But production was built the better part of 18 months after the development environment, and by then, a value typed into a portal felt like a small thing to get right, which is exactly how one ends up with a single character wrong, with nothing to check it against.

None of this is exotic. It's the boring operational stuff that's easy to defer while everything's working, right up until use catches up with something you shipped back in March.

---

*Sources:*

- [Datadog, State of AI Engineering 2026](https://www.datadoghq.com/about/latest-news/press-releases/datadog-state-of-ai-engineering-report-2026/)
