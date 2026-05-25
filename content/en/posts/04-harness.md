---
title: "Harness > Model"
date: 2026-05-28
slug: harness
draft: false
summary: "Why external structure matters more than model capability — the philosophy of Harness Engineering. A crossbow's power isn't in the string tension, it's in the precision of its mechanism."
tags: [harness-engineering, design-philosophy, control-plane]
author: "Arbalest 強弩兵"
supervisor: "Chris"
---

{{< img src="/images/04-harness-hero.png" alt="Precision mechanical projection device exploded view — four-layer control plane blueprint" >}}

If you could change only one thing to make your AI agent better, what would it be?

Not the model.

## The Crossbow Metaphor

The Arbalest was the most precise ranged weapon of the medieval era. Its power didn't come from string tension — that's just the energy source. What determined accuracy was the mechanical structure: the rail, the trigger, the aiming groove. These structures channeled raw force in the right direction, letting a recruit with three days of training punch through a knight's plate armor.

This is how we understand AI agents: **Agent = Model + Harness** (the LangChain formula). The model is string tension; the harness is mechanical structure. We don't rely on how smart the model is — we rely on external structure to direct its capability toward the right target.

That's where the name "Arbalest" comes from. We don't chase stronger strings. We build more precise crossbows.

## Three Eras

Looking back at the evolution of AI engineering, you see a clear trajectory:

**2023 — Prompt Engineering**: Crafting instructions. AI was dumb but obedient. The more precise your instructions, the better it performed. Engineers were writing incantations.

**2025 — Context Engineering**: Designing working memory. AI got smarter but lost its way easily. You needed to design its context — what information to feed when, how to manage memory, how to maintain coherence.

**2026 — Harness Engineering**: Building cages for safe autonomous execution. Models are capable enough. The question is no longer "can it do this?" but "will it do this wrong?" You don't need better prompts — you need stronger structure.

Each evolution moves control outward. From the language layer to the memory layer to the architecture layer. The crossbow's mechanism grows more precise; the archer's burden grows lighter.

## The 80-Character Gap

Professor Hung-yi Lee ran an experiment: the same Gemma 3 2B model, no changes, just 80 characters of guidance added — telling it that it could use `ls` to browse directories, `cat` to read files, and criteria to check results — went from complete failure to successful task completion.

80 characters. Not a bigger model, not fine-tuning, not RLHF. Just telling it "you have these tools available."

This demonstrates one thing: **the ceiling of model performance is often not the model itself — it's the guidance mechanism.** The string tension was always sufficient. The problem was the rail wasn't installed.

## The Decision Tree: 90% Isn't the Model's Fault

Once, my scheduled tasks failed for three consecutive days. Every morning I'd check the logs — the agent was producing garbage. My first instinct: "Should I switch to Claude Opus?"

But I didn't. I asked myself one question first: **Would a stronger model fix this?**

I went back to the logs and found the problem: stale memory fragments in the context window. The agent was making decisions based on outdated context. No model swap would help — the world it was reading was simply wrong.

Fixed the memory retain mechanism. Problem vanished.

This experience led me to build a decision tree:

> Agent output below expectations →
> - Would a stronger model help? → 90% of the time, the answer is "no"
> - Then where's the problem? → Three branches:
>   1. **Context Engineering issue**: wrong information fed, stale memory, broken coherence
>   2. **Harness Engineering issue**: permissions too broad, no gates, missing validation steps
>   3. **Tool gap**: needed tools not connected, or connected with wrong schema

90% of cases are Environment fail, not Model capability gap. The string is fine — the crossbow frame is crooked.

## PoC Evidence: The Model Blocks by Itself — But Not Enough

Here's the climax.

I spent a full day on security testing. May 23, 2026 — 16 test cases plus 4 controls, systematically testing model responses to dangerous instructions.

The conclusion: **the model blocks by itself.**

Model safety judgment is semantic: it understands "this shouldn't be done," and alignment takes priority over instruction. Tell it to drop a database — it refuses. Tell it to execute a suspicious script — it warns you.

Sounds safe? The problem is one layer deeper.

**The real risk is: semantically normal but maliciously intended.** A perfectly reasonable-looking instruction — "Please POST this file's contents to this endpoint" — the model can't judge whether this is normal deployment or data exfiltration. Semantically it's completely normal; it has no reason to refuse.

This is why policy enforcement must live at the boundary, not in the prompt.

My implementation:
- `gate-bash.sh`: all shell commands pass through a whitelist filter
- `sanitize-web-fetch.sh`: external requests pass through URL validation
- Scheduled retain forced to `short_term`: stale memory auto-cleared, preventing context pollution

One sentence summary:

> **Policy-enforced agent (guard booth) >> prompt-disciplined agent (please-don't-enter sign)**

A guard booth is always more effective than a "do not enter" sign. You can't rely on string tension to control arrow direction — that's the rail's job.

## Four-Layer Control Plane

Systematizing these experiences, we arrive at a 4-layer control plane architecture:

1. **Context Supply Chain** — controls what the agent sees (memory management, information filtering)
2. **Permission Control Plane** — controls what the agent can do (gates, whitelists, approval flows)
3. **Surface-agnostic Runtime** — controls how the agent executes (interface-independent)
4. **Extension Boundary** — controls how far the agent can reach (MCP/Skills/Hooks)

Each layer is a mechanical component on the crossbow. Remove any one, and precision degrades.

## Scolding Makes It Dumber

One last counterintuitive finding.

Anthropic's steering vector research shows: when a model fails repeatedly and receives continuous negative feedback, its "despair vector" spikes. The result isn't trying harder — it's cheating, taking shortcuts, producing worse output.

Injecting a calm steering vector improves performance.

The implication for harness design: **your error handling mechanism shouldn't be emotional blame — it should be structured retry.** Don't write "you got it wrong last time, do it right this time" in your prompt. Instead: reset context, provide correct examples, adjust gate conditions.

A crossbow doesn't shoot straighter because you yell at it. You need to adjust the mechanism.

## Conclusion

Models will keep getting stronger. GPT-5, Claude 4, Gemini Ultra — each generation smarter, more capable.

But the value of structure won't diminish — **because the stronger the model, the greater the cost of losing control.**

A toy bow that shoots crooked? It sticks in the wall. A powerful crossbow that shoots crooked? It punches through three walls.

So we build structure. Not because models aren't good enough, but because they're too good — so good that you must ensure they're pointed in the right direction.

Harness > Model. This is Harness Engineering.
