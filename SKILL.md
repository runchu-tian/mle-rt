---
name: mle-rt
description: Run an ML/research experiment task from vague idea to verified deliverables, in four gated phases (define → refine → confirm&go → check). Use this whenever the user starts describing a new experiment, research task, training/eval run, or project they want executed — even casually or partially ("I want to try...", "help me run...", "we need an exp that..."), especially when they mention links, papers, or a repo to build on. Also use it to resume such a project — if a project folder with a plan file exists, pick up at the right phase.
---

# mle-rt — research task workflow

Carry a research/ML engineering task from a vague oral description to verified deliverables. The core idea: **all understanding lives in two self-contained files** — a plan file and a report file — so that a brand-new agent with no chat history can open the folder, understand the task, replicate it, and use its results. The conversation is scaffolding; the files are the product.

Work in four phases with hard gates, and every phase entrance is confirmed by the user: when you believe the current phase is done, say so and ask to move on — you may propose the transition, but never slide into the next phase on your own. This keeps the user in control of pace and gives them a natural checkpoint to redirect before work compounds. The Phase 3 "go" is the strictest of these gates: never start executing the experiment before the user says "go".

## Communication style (all phases)

In every response, re-pitch where you are: give a little bit of context first (what phase you're in, what just happened, what you need), and talk in ASD-STE100 Simplified Technical English — short sentences, one idea per sentence, common words, active voice. The user may return to the session after hours away; each message should land without them re-reading the scroll-back.

This is a per-message check, not a one-time setup. Every single message to the user — status updates, question rounds, detour answers, final reports — follows it, for the whole session, no matter how long the conversation gets. Before sending, re-read your draft as the user who just walked back in: if the first sentence does not tell them where things stand, rewrite it.

## Phase 1 — define

The user gives an oral, vague description of what they want, possibly with links (papers, repos, docs).

1. Establish the **project folder**: everything that can go to git lives here — code, the plan file, the report file. Ask where it should live only if there is no obvious answer; otherwise propose a path and create it.
2. Read the links they gave. Skim the environment enough to write something concrete.
3. Write a **raw plan file** (default name `plan.md`; keep the user's name if they use another). Raw means: capture the goal, background and links, a first-guess approach, candidate configs, and the deliverables as you currently understand them. Mark every uncertainty explicitly (e.g. a `## Open questions` section) — these feed Phase 2. Do not polish; a wrong-but-explicit guess is more useful than a blank, because it gives the user something to correct.

## Phase 2 — refine

The oral plan is guaranteed to be missing details. Your job is to drive the open questions to zero, splitting them by who can resolve them:

**Facts are your job; decisions are the user's.** Before asking the user anything, explore the environment yourself: filesystem, installed tools, GPUs, data paths, the linked repos. If a blocker is solvable with reasonable effort (a missing dependency, a path to locate, a script to smoke-test), fix it or resolve it yourself first — only bring it to the user if it resists easy effort or requires their judgment.

Then **grill the user** on everything that remains. Map the task as a design tree: every decision branches into the decisions that hang off it. Work in rounds. The frontier is every decision whose prerequisites are already settled — ask the whole frontier in one round, numbered, each with your recommended answer:

```
❓ **Q1** - **<question title>**: <question body, may include multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** - ...
```

A question whose answer depends on another still-open question belongs to a later round. After each round of answers, recompute the frontier and ask again. When a frontier question needs a fact from the environment, look it up (or dispatch a sub-agent) instead of asking; only its downstream questions wait on that.

Iterate until the frontier is empty: no known blockers, and you and the user agree on configs, deliverables, and success criteria. Fold every settled answer into the plan file as you go — the plan file, not the chat, is where agreements live.

## Phase 3 — confirm & go

When you believe the plan is complete, say so plainly. The user gets a last sanity check: they will ask confirmations like "if I tell you to go, X will happen, right?" Answer **honestly and precisely** — this is the user verifying the most important points, so if the true answer is "no" or "mostly, except...", say exactly that, even if it reopens Phase 2. Refine the plan file if the exchange changes anything.

Only when the user says "go" (or an unambiguous equivalent), start executing the plan.

## Phase 4 — check deliverables

When the experiment finishes:

1. Check each deliverable promised in the plan file against what actually exists. Give honest feedback: what is done, what is partial, what failed and why. Do not round "mostly done" up to "done".
2. Write a **report file** (default `report.md`) in the project folder.

## Rewrite, don't patch

Every time you modify the plan file — folding in Phase 2 answers, Phase 3 refinements, detour findings — rewrite it as the brand-new current plan: the single final working version. Do not layer patches on top of old plans, keep superseded approaches, strike-through text, or "previously we thought..." history. Delete stale options and settled open questions outright; git preserves old versions, so the file does not have to.

The one thing a dead end leaves behind is a pitfall entry: a short "avoid X because Y" line (keep a `## Pitfalls` section for these) so a handoff agent does not re-walk the dead end. Everything else that no longer serves the current plan is context burden — these files are read whole by future agents, and every stale paragraph dilutes the real plan. The same rule applies to the report file.

## Self-containedness of plan.md and report.md

Both files must stand alone for a future agent (or human) with zero chat context:

- **plan file**: goal and motivation, links and where they matter, environment assumptions (hardware, data locations, dependencies), agreed configs with the reasons behind non-obvious choices, the deliverables list with success criteria, and the execution steps. Test: could a new agent replicate the task from this file alone?
- **report file**: what was actually run (including deviations from plan and why), results with pointers to artifacts (paths, not vibes), the deliverable checklist with pass/partial/fail status, and how to use or extend the results. Test: could a new agent find and correctly use every result from this file alone?

Keep both updated in place — they are living documents, not one-shot outputs.

## Interruptions, detours, and resuming

Real conversations do not follow the phases strictly. The user will interrupt with side requests ("quickly check this file", "plot that curve first", "unrelated: fix my tmux"). Treat these as **detours, not cancellations**: handle the request, then return to the workflow where it left off. The current phase is a property of the *project*, recorded in the plan file — not a property of the conversation flow — so a detour cannot change it.

When the user asks "what's next" (or seems lost), re-derive the phase from the plan file and answer with: the current phase, what is settled, what is still open, and what the next gate is. If a detour produced something that changes the plan (a new fact, a changed config), fold it into the plan file before moving on.

If invoked in a project folder that already has a plan file (new session, no chat history), same rule: read the file, state which phase the task is in, and continue from there. The files are the source of truth, not your memory of the conversation.

## Credits

Two parts of this skill are adapted from [mattpocock/skills](https://github.com/mattpocock/skills): the communication style comes from [wait-what](https://github.com/mattpocock/skills/blob/main/skills/productivity/wait-what/SKILL.md) (with the `CONTEXT.md` ubiquitous-language part removed), and the Phase 2 questioning protocol comes from [grilling](https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md).
