---
title: "Working with LLMs in Software Development: It's Not About the Prompt—It's About the Process"
description: "Practical lessons from using language models in day-to-day software development—from framing the task to verifying the output."
date: 2026-06-20
tags: [LLM, software development, AI-assisted coding, engineering practice]
reading_time: 12 min
---

# Working with LLMs in Software Development: It's Not About the Prompt—It's About the Process

*Language models can produce code faster than any human. Paradoxically, that's exactly why frustration with them rarely stems from "AI being dumb"—it stems from a weak process. We hope for magic when what we actually need is clarification, alignment, and verification.*

Over the past few years LLMs have outgrown their role as fancy autocomplete. Agentic environments like Cursor, Copilot Workspace, and their peers can read an entire repository, run tests, and modify dozens of files in a single pass. Velocity has gone up—and so has the cost of getting it wrong: a confident explanation in chat can sit right next to a diff that silently breaks an adjacent module.

This article distills practical experience on structuring your work with a model so it genuinely speeds up development rather than creating the illusion of progress. No "top 50 prompts" listicles, and no claims that AI will replace engineers.

---

## The core idea

**An LLM is not a substitute for a developer. It's an accelerator—but only when the process around it is sound.**

Models excel at drafting, exploring options, grinding through routine, and searching the codebase. Humans remain accountable for the goal, the constraints, architectural decisions, change review, and whatever ships to production. The more precisely you frame the task, surface blind spots, and run verification, the fewer rewrites you'll face—and the safer the speed boost becomes.

---

## 1. Talk like a person—but be specific

Models are trained on natural language, and bureaucratic fluff hurts them just as much as it hurts any colleague. "Implement an optimal solution following best practices" conveys almost zero useful information about priorities.

What works is plain speech plus explicit boundaries:

> "I need an endpoint for exporting notes. Don't change the DB schema and don't touch the frontend. Give me a five-point plan first, then code."

"Natural" doesn't mean "fuzzy." Think of it as talking to a teammate who can't read your mind: you state *why*, *what's in scope*, and *what's off limits*.

Don't hesitate to interrupt:

> "I didn't follow that—walk me through how it works, as if I had no prior context."

Curiosity in a dialogue with a model isn't a sign of weakness; it's insurance against blind copy-paste.

---

## 2. Keep clarifying until you agree—on both understanding and approach

The most expensive mistake is silently accepting a plan you interpreted differently. Five minutes of paraphrasing is far cheaper:

> "Let me make sure I've got this: you're proposing to cache X in Redis because of Y, and we're deferring the in-memory approach because of Z?"

But alignment isn't just about problem definition. It's equally important to ask: **is this actually the right path?** Models happily offer the first plausible solution. Your job is to surface alternatives and weigh the "cost of the question": implementation complexity, lock-in, migration overhead, maintenance burden two years out, and behavior at your actual data volumes.

> "Compare libraries A and B for our scenario: 500 GB of data, nightly batch processing, Python 3.13. Which one will be easier to live with two years from now?"

---

## 3. Ask the questions only you can ask

No task description captures everything: terabytes of source data, a read-only NAS mount, company security policy, a legacy module nobody dares touch. The model will almost never ask about these things unprompted.

This is precisely where human input delivers the most value. One well-placed question:

> "Bear in mind the source files are on a NAS, the path is read-only, and the index lives in PostgreSQL. How does that change the plan?"

—can reshape the entire approach. These aren't nitpicks; they're standard engineering due diligence when pairing with AI.

---

## 4. Check freshness: versions, APIs, docs

A model can confidently recommend an API that doesn't exist or a function signature pulled from a year-old tutorial. This isn't a flaw in your prompting—it's an inherent property of a tool whose world knowledge is both limited and lagging.

Build a habit of explicitly requesting verification:

> "Verify the signatures against the current FastAPI 0.115 docs and our `pyproject.toml`. Don't use any deprecated parameters."

Even then, **run the code**. A persuasive explanation in chat is no substitute for the compiler, the test suite, or the linter.

---

## 5. Settle architecture before the big diff

Agentic environments can touch many files in one shot. Without upfront alignment on structure, the resulting diff will surprise you: unnecessary abstraction layers, misdrawn module boundaries, unintended changes to adjacent features.

A reliable sequence:

1. Describe the components, data flows, and failure points.
2. Agree on the plan.
3. Implement in **small steps**—not with a single "redo everything."

> "Before you write any code, lay out the components and data flows. I want to align on the design first—then we'll implement step by step."

Long-lived decisions are worth recording in a short note or an ADR—both for the team and for future model sessions.

---

## 6. Configure the environment for the human + model pair

A common mistake is optimizing the setup exclusively for your own comfort. In agentic mode what matters is that the model can **see the project** and **validate its own output**: shared build and test commands, terminal and git access, a legible repository layout, and project conventions stored in files (`AGENTS.md`, rules, skills)—not buried in chat history alone.

When setting up tooling, discuss not just "how to run things" but the broader conditions the project lives under: cross-platform **development** environments (don't confuse these with the product's target platforms), i18n requirements, documentation language and format, and team conventions.

Keeping IDE extensions to a minimum tends to pay off: less noise, fewer context collisions. But remember that the workstation isn't just a prompt box. It's the full loop: **dialogue, diff review, and running checks**.

Cloud-hosted models and tools change more often than you'd expect, even when the interface looks the same. Revisit your practices periodically—what worked six months ago may demand a different workflow today.

---

## 7. Be honest about who owns what

A popular fantasy: "The model does the work and I just make requests." In practice, ownership breaks down differently:

| Area | Owner |
|------|-------|
| Goal, priorities, "why" | Human |
| Constraints, risks, domain knowledge | Human |
| Drafts, options, routine work | Model |
| Codebase search, error-fix iterations | Model |
| Diff review, final merge/reject decision | Human |
| Production accountability | Human |

A rule worth adopting as team policy: **a change isn't accepted until checks are green**—tests, linter, build, or an explicitly agreed manual scenario. For experiments you can relax the bar; for auth and data handling, tighten it.

Read the **diff**, not just the chat reply. The chat can sound perfectly reasonable while the adjacent module is already broken.

---

## 8. A minimal working loop for one task

Compressed into a short cycle, the practice looks like this:

1. **Goal + boundaries + constraints**—in plain language.
2. **Plan and architecture**—reach agreement before any code.
3. **Implement one small step**—one coherent result at a time.
4. **Diff + tests + linter**—mandatory checkpoint.
5. Something unclear → ask for an **explanation**; roll back or revise if needed.
6. **Commit or PR**—with a clear "why," not just a "what."

State the **risk level** up front: "rough draft for an experiment" and "changing auth in production" call for very different review depths and test coverage.

---

## What to avoid

A handful of anti-patterns that most reliably turn an LLM from helper into liability:

**One massive prompt.** "Rewrite the module, add a migration, update the UI, and deploy"—a textbook recipe for regressions. Break the work into steps with checkpoints.

**Trusting a polished explanation.** Models are persuasive even when wrong. Running tests is not optional.

**Keeping all context in your head.** Anything you don't say, the model can't know. Capture recurring context in the repository; voice task-specific constraints explicitly.

**Blind trust in suggested dependencies.** A new library "recommended by AI" is a supply-chain risk. Ask about alternatives.

**Secrets in prompts.** Keys and passwords belong in `.env` files and under company policy—never in chat.

**Arguing without evidence.** Start with logs, test results, and documentation—then refine the request.

---

## 9. Two modes: when you need a spec vs. when one prompt will do

Day-to-day work with LLMs doesn't fit a single workflow. In practice, two distinct modes emerge—and both are legitimate.

**Spec mode** is for tasks touching 3+ files, involving data migrations, changing an API contract, or requiring explanation to colleagues later. Full cycle: formalized requirements, design, decomposition into atomic steps, tests as acceptance criteria.

**Ad-hoc mode** is for single-file fixes, typos, and small refactors. One sentence describing the problem → fix → tests → commit with a meaningful message.

The critical skill is **recognizing the tipping point**. If an ad-hoc fix has grown to three files or now needs a migration, that's your signal to stop and switch to planned mode. The converse is equally true: writing a full spec for a one-liner is overhead with no payoff.

The biggest trap in ad-hoc mode is commit messages. `fixes` and `bug fix` feel clear at typing time and become useless a week later. Even in fast mode, a single line of "what and why" (`fix: normalize backslash separators in export path matching`) pays for itself the first time you run `git bisect`.

---

## 10. Managing session length

Agentic IDEs create a sense of flow—changes arrive fast and the conversation never stops. But after 2–3 hours of uninterrupted work, attention to diffs predictably drops. The telltale symptom: a chain of five `fix:` commits in one evening, each repairing a bug introduced by the one before.

Practical guidelines:

- **One work block** = 1–2 hours of focused effort on a single feature. Take a break between blocks.
- **Stop signals**: the third consecutive fix on the same commit; the urge to type just `fixes`; inability to explain what the latest diff actually does.
- **When you stop**: save current state, jot down "where I am and what's next," then come back after a break and start by reviewing your own diff from the session.

A cascade of fixes isn't "the model being dumb." It's the price of accepting the first commit without adequate verification. If a second fix is needed within an hour, it's usually more efficient to roll back and redo the original commit properly than to keep patching on top.

---

## 11. Solo development: when there's no reviewer

"Get a second pair of eyes" falls apart when you're the sole developer. In that case, process has to fill the gap left by the absent reviewer.

**"The morning after."** Don't merge a feature branch on the same day you wrote it. Overnight your brain reorganizes context; in the morning you read your own diff almost as if it were someone else's code, and you catch things you missed in the heat of the moment.

**Automated tests as a behavior reviewer.** Without human review, property tests become your critical safety net. They exercise scenarios you didn't think of during implementation.

**LLM as an extra pair of eyes (with caveats).** You can ask the model in a separate session to review the diff—but keep expectations in check: it doesn't know your business context, and it may miss logical errors that happen to compile cleanly. Treat it as an additional signal, not a replacement for review.

**The two-hour rule.** Leave at least two hours between the last commit on a branch and merging into `main`. In that window, tests run, your mind cools off, and you can read `git diff main..HEAD` as one cohesive change.

---

## When an LLM is the wrong primary tool

Honesty matters—for team trust, too. A language model is a poor lead executor when you need:

- legally binding language without a lawyer's involvement;
- safety-critical logic without formal verification;
- incident investigation without logs and reproducible facts.

In those situations AI can still help as a **drafting aid or sounding board**, but the decision and the process stay with humans.

---

## Conclusion

Effective work with LLMs looks nothing like a quest for the "perfect prompt." It's an engineering discipline: a clear goal, clarifying questions, architectural alignment, an environment where output can be verified, and human accountability for what ends up in the repository and in production.

If you had to distill this for a team in one sentence:

> **Dialogue and verification accelerate development; prompt magic without verification only accelerates mistakes.**

---

## Quick-reference phrase sheet

| Situation | Example |
|-----------|---------|
| Checking understanding | "Let me confirm: … because …, and we're skipping … because …?" |
| Task boundaries | "Do X. Don't touch Y or Z. Plan first, then code." |
| Alternatives | "Is there a simpler path? What's the long-term maintenance cost?" |
| Domain nuance | "The data lives on a read-only NAS. How does that affect the approach?" |
| API freshness | "Cross-check against the docs for version N and our lockfile." |
| Before coding | "Lay out the architecture and risks. Code comes after alignment." |
| After the patch | "Run tests and linter. If anything fails, fix it and report back." |

---

*Based on hands-on experience developing with agentic IDEs and language models. June 2026.*

*Related internal guide with checklists: [llm-development-practices.en.md](./llm-development-practices.en.md).*
