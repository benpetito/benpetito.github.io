---
title: "Using AI to Surface Insights From Two Decades of Audit History"
date: 2025-05-13T09:00:00+09:30
description: A proof of concept using local LLMs to generate useful audit summaries from nearly twenty years of structured data.
menu:
  sidebar:
    name: AI Audit Summaries
    identifier: ai-audit-summaries
    weight: 40
tags: ["AI", "Enterprise", "Data"]
categories: ["Engineering"]
---

A couple of months ago, I met with a client to explore how AI might help streamline some of their internal processes. Their application contains nearly two decades of audit history, yet despite the volume of data, it wasn't being actively used.

Last week, they reached out with a specific request: could we generate a summary of the past three years of audits so that auditors could quickly understand recurring issues and common themes when preparing for an upcoming audit?

## The approach

I focused on extracting the auditor's comments for each business audited in the last three years. Most businesses receive one or two audits annually, and each audit typically spans one to three documents. The system is built on [Skyve](https://skyve.org/), which makes querying metadata straightforwardly accessible — I had around 29 comment fields per audit to work with.

For the model, I used a local LLM because the audit data was commercially sensitive. I evaluated a couple of options. Qwen 3 had just dropped and I was keen to try it, but it's a reasoning model and produced too much "thinking out loud" for what I needed. I settled on Microsoft's newly released Phi 4, which performed well.

## Prompt chaining

Around the same time, Google's AI team released a new prompting guide validating something I'd already seen in practice: prompt chaining — breaking tasks into smaller, sequenced prompts — often performs better than a single complex prompt.

The chain I ended up with:
1. A first prompt generates a summary from the 29 comments across each individual audit.
2. A second prompt acts as a reviewer, ensuring the output is coherent and useful.
3. A third prompt takes the summaries from the last three years and produces an overall summary per business.

## Results

After some light testing, I let it run. The results genuinely exceeded expectations — accurate, concise, and actually useful for the auditors' workflow.

Performance was the main constraint. Phi 4 brought my laptop to its knees, but it did the job. The full run took around 5 minutes per business — fast enough for an overnight batch; not suitable for anything real-time.

## What comes next

If this moves to production, I'd want to add safeguards to verify that summaries accurately reflect the source content. I'd also do more model evaluation — the local LLM constraint might not always apply, and there may be better options available on Azure without the data residency concerns.

Many of our customers' applications are data-intensive and contain a lot of untapped history. There's real value in making that data more useful — not just archiving it indefinitely.
