# mle-rt

[![skills.sh](https://skills.sh/b/runchu-tian/mle-rt)](https://skills.sh/runchu-tian/mle-rt)

A [Claude Code skill](https://code.claude.com/docs/en/skills) that runs an ML/research experiment task from vague idea to verified deliverables, in four gated phases:

1. **define** — you describe the task orally (links welcome); the agent sets up a git-able project folder and writes a raw `plan.md` with explicit open questions.
2. **refine** — the agent explores the environment and fixes solvable blockers itself (facts are its job), then grills you in rounds of numbered questions with recommended answers (decisions are yours), folding every agreement into `plan.md`.
3. **confirm & go** — you get a final sanity check ("if I say go, X happens, right?"); execution starts only on an explicit "go".
4. **check** — deliverables are checked honestly (pass / partial / fail, no rounding up) and a self-contained `report.md` is written.

`plan.md` and `report.md` are self-contained by design: a new agent (or human) with zero chat history can understand, replicate, and reuse the project from those files alone. Interruptions are detours, not cancellations — the phase lives in the plan file.

Responses follow a re-pitch style (brief context first, ASD-STE100 Simplified Technical English), adapted from mattpocock's [wait-what](https://github.com/mattpocock/skills/blob/main/skills/productivity/wait-what/SKILL.md); the questioning protocol is adapted from his [grilling](https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md) skill.

## Install

Works with Claude Code, Codex, and other agents via [skills.sh](https://skills.sh):

```bash
npx skills@latest add runchu-tian/mle-rt
```

Add `-g` to install for all projects instead of just the current one. Update later with `npx skills update`.

<details>
<summary>Manual install (git clone)</summary>

Personal (all projects):

```bash
git clone git@github.com:runchu-tian/mle-rt.git ~/.claude/skills/mle-rt
```

Project-scoped: clone into `<project>/.claude/skills/mle-rt` instead. Update with `git pull`.

</details>

## Use

Start describing an experiment in any Claude Code session (the skill auto-triggers), or invoke it explicitly with `/mle-rt`. Once per session is enough. To resume an existing project, point Claude at the project folder — it reads `plan.md` and picks up at the right phase.

## Evals

`evals/evals.json` holds the test prompts and assertions used to benchmark the skill (with-skill vs. no-skill baseline).
