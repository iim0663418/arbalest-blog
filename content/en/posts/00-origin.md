---
title: "Origin"
date: 2026-05-25
slug: origin
draft: false
summary: "Why I built my own agent memory system, and what this series sets out to answer."
tags: [agent-memory, arbalest]
---

## An Unexpected Cross-Reference

Arbalest (強弩兵) is a personal AI agent system I've built over 100+ specs. One of its core components is agent-memory — a memory layer running on Cloudflare Workers + D1 + Vectorize + R2.

A few weeks ago I came across warmwater.dev's 8-part series systematically dissecting existing agent memory solutions: Mem0, Letta, Zep, and others. Solid analysis framework, good coverage of dimensions. I cross-referenced it against my own design to see where we overlap and where we diverge.

The result surprised me.

It wasn't divergence. It was wholesale absence. Several dimensions I consider most critical in my design simply don't exist in their analytical framework:

- **Biomimetic sleep cycles** — NREM/REM-driven memory consolidation and pruning
- **Active Inference driven uncertainty management** — not passive storage, but active prediction and correction
- **Perception-action loops** — memory isn't a database query, it's part of a perception-action loop
- **3-layer compute separation** — L1 deterministic logic / L2 local AI / L3 cloud inference, each with different memory access patterns
- **Outcome signal feedback** — writes aren't the end; outcome signals determine a memory's subsequent fate
- **Memory security and injection defense** — when memory itself can be attacked, what defenses do you need

This isn't a criticism of warmwater.dev's analysis. They analyzed what existing systems actually do. And existing systems genuinely don't do these things.

## The Question This Series Answers

In one sentence: **Facing the same problems, why did I make different choices, and what do those choices look like running in production?**

I'm not writing an academic paper. I'm not doing product marketing. This is closer to an engineer's workshop notebook — documenting the context behind design decisions, the pitfalls encountered during implementation, and the behaviors observed once things are running.

Some decisions look right in hindsight. Some I'm still not sure about. Some might be over-engineering. But they're all running, and there are real traces to look at.

## Why Now

Arbalest's agent-memory has been running stably for a while now. Sleep cycles are executing, Active Inference is correcting predictions, R2 archival is compressing history. I have enough production data to tell concrete stories rather than just theory.

At the same time, I've noticed that public discussion around agent memory mostly stays at "how to store" and "how to retrieve." Very few people talk about memory lifecycle, memory agency, memory security. These are the gaps I want to fill.

## Series Roadmap

This series is planned as eight posts:

1. **Why not use existing systems** — limitations of Mem0/Letta/Zep, and what I need that they can't provide
2. **Write strategy** — not everything is worth remembering; the logic behind write decisions
3. **Lifecycle design** — the complete path from memory birth to death
4. **R2 archival** — compression, storage, and resurrection of cold memories
5. **Biomimetic sleep** — how NREM/REM cycles drive memory consolidation
6. **Active Inference** — managing memory uncertainty with the free energy principle
7. **Memory security** — when attackers try to poison your memories
8. **Evolution map** — how the system grew from v0 to now, and where it goes next

Every post will include actual architecture fragments, configurations, or production observations. No fabricated examples.

Let's begin.
