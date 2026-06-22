---
title: "How to Work with LLMs in Software Development: Not Prompt Magic, but Dialogue Discipline"
description: "Practical experience using language models in software development—from framing the task to verifying the result."
date: 2026-06-20
tags: [LLM, software development, AI-assisted coding, engineering practice]
reading_time: 12 min
---

# How to Work with LLMs in Software Development: Not Prompt Magic, but Dialogue Discipline

*Language models can write code faster than humans. But that is exactly why disappointment usually comes not from “AI stupidity,” but from a weak process: we expect magic where what we really need is clarification, alignment, and verification.*

Over the past few years, LLMs have stopped being toys for line-by-line autocompletion. Agentic environments such as Cursor, Copilot Workspace, and similar tools can read a repository, run tests, and change dozens of files from a single request. Speed has increased—and with it, the cost of mistakes: a polished explanation in chat can sit right next to a diff that breaks a neighboring module.

This article condenses practical experience on how to structure work with a model so that it truly accelerates development instead of creating an illusion of progress. No “top 50 prompts,” and no promises that AI will replace engineers.

---

## The main point

**LLMs are not a replacement for developers; they are force multipliers when paired with a good process.**

A model is useful for drafts, options, routine work, and codebase search. Humans remain responsible for the goal, the constraints, architectural decisions, review of changes, and whatever goes into production. The more clearly you frame the task, catch blind spots, and run verification, the fewer rewrites you will need—and the safer the acceleration becomes.

---

## 1. Speak like a human—but do not be vague

Models are trained on natural language, and bureaucratic wording with empty abstractions usually hurts them just as much as it hurts developers. A phrase like “implement an optimal solution according to best practices” says almost nothing about priorities.

A workable style is plain language plus explicit boundaries:

> “We need an endpoint for exporting notes. Do not change the database schema and do not touch the frontend. First give me a five-point plan, then code.”

“Natural” here does not mean “imprecise.” It is a conversation with a colleague who does not have access to your head: you explain *why*, *what is allowed*, and *what is off limits*.

And do not hesitate to stop the flow:

> “I didn’t follow that—explain how it works as if I had not seen the context.”

Curiosity in a conversation with a model is not weakness; it is insurance against blind copy-paste.

---

## 2. Clarify until you align—both on understanding and on approach

The most expensive mistake is silently agreeing to a plan you understood differently. It is cheaper to spend five minutes paraphrasing:

> “Am I understanding you correctly: you want to cache X in Redis because of Y, and we are postponing the in-memory option because of Z?”

But alignment is not only about the wording of the task. It is also worth asking: **is this approach actually worth it?** A model is happy to offer the first plausible path. Your role is to surface alternatives and evaluate the “cost of the question”: implementation complexity, lock-in, migrations, maintenance two years from now, and behavior under your real data volumes.

> “Compare libraries A and B for our use case: 500 GB of data, nightly batch, Python 3.13. Which one will be easier to live with in two years?”

---

## 3. Ask the questions that matter specifically to you

Not everything fits into the task description: terabytes of source files, a read-only NAS, company policy, a legacy module that cannot be touched. The model will rarely ask about these things on its own—unless you bring them up.

This is exactly where humans usually create the most value. One precise question:

> “Keep in mind that the source files live on a NAS, the path is read-only, and the index is in PostgreSQL. How does that change the plan?”

—can completely reshape the implementation plan. Those clarifications are not nitpicking; they are normal engineering work when pairing with AI.

---

## 4. Verify freshness: versions, APIs, documentation

A model may confidently suggest an API that does not exist, or an outdated signature from last year’s tutorial. That is not a bug in your prompt—it is a property of a tool whose knowledge of the world is limited and lagging.

A helpful habit is to explicitly ask for a cross-check:

> “Check the signatures against the current FastAPI 0.115 documentation and our `pyproject.toml`. Do not use deprecated parameters.”

But even then, **run the code**. Convincing reasoning in chat is not a substitute for the compiler, the tests, or the linter.

---

## 5. Discuss architecture before a large diff

Agentic environments can change many files in one pass. If you do not align on structure first, the diff will surprise you: extra layers, wrong module boundaries, or adjacent functionality changed unintentionally.

A workable order looks like this:

1. Describe the components, data flows, and failure points.
2. Align on the plan.
3. Implement in **small steps**, not with a single “redo everything.”

> “Before writing code, describe the components and the data flows. I want agreement first—then step-by-step implementation.”

Long-lived decisions are worth capturing in a short note or ADR—for the team and for future sessions with the model.

---

## 6. Set up the environment for the “human + model” pair

A common mistake is optimizing only for your own convenience. In agentic mode, it matters that the model can **see the project** and **verify itself**: shared build and test commands, terminal and git access, a clear repository structure, and project rules stored in files (`AGENTS.md`, rules, skills), not only in chat history.

When configuring tools, it is worth discussing not just “how to run things,” but also the conditions the project lives under: cross-platform **development** (not to be confused with the product’s target platforms), i18n, documentation language and format, and the team’s conventions.

Keeping IDE extensions to a minimum is usually wise: less noise, fewer context conflicts. But the workstation is not just a prompt window. It is **dialogue, diff review, and running checks**.

Cloud models and tools change more often than it seems, even when the interface still shows “the same” version. Revisit your practices periodically—what worked six months ago may now require a different workflow.

---

## 7. Distribute responsibility honestly

A popular illusion is: “the model works in the environment, and I just ask.” In practice, the roles are distributed differently:

| Area | Who leads |
|------|-----------|
| Goal, priorities, “why” | Human |
| Constraints, risks, domain | Human |
| Drafts, options, routine work | Model |
| Codebase search, error-fix iterations | Model |
| Diff review, final “merge / do not merge” | Human |
| Responsibility for production | Human |

A rule worth turning into team policy: **a change is not accepted until checks are green**—tests, linter, build, or an explicitly agreed manual scenario. For experiments you can relax the bar; for authentication and data changes, you should raise it.

Look at the **diff**, not just the answer in chat. The chat may sound reasonable while the neighboring module is already broken.

---

## 8. The minimal working cycle for one task

If you compress the practice into a short loop, it looks like this:

1. **Goal + boundaries + constraints**—in plain language.
2. **Plan and architecture**—agreement before code.
3. **Implement one small step**—one meaningful result at a time.
4. **Diff + tests + linter**—a mandatory pause.
5. If something is unclear—ask to **“explain”**; roll back or revise if needed.
6. **Commit or PR**—with a clear “why,” not just a “what.”

State the **risk level** explicitly: “draft for an experiment” and “changing production authentication” require very different review depth and very different test coverage.

---

## What to avoid

A few anti-patterns most often turn an LLM from a helper into a liability:

**One huge prompt.** “Rewrite the module, add a migration, update the UI, and deploy it” is a classic source of regressions. Break the work into steps with checkpoints.

**Believing a polished explanation.** Models are persuasive even when wrong. Tests and execution are not optional.

**Keeping all context in your head.** What you do not say, the model will not guess. Capture recurring context in the repository; speak out task-specific constraints.

**Blind trust in dependencies.** A new library “recommended by AI” is a supply-chain risk. Ask about alternatives.

**Secrets in prompts.** Keys and passwords belong in `.env` files and company policy, not in chat.

**Arguments without facts.** Start with logs, tests, and documentation—then refine the request.

---

## 9. Two modes: when you need a spec and when one prompt is enough

Real work with LLMs does not fit into a single process. In practice, there are two modes—and both are legitimate.

**“Spec” mode** is for tasks that touch 3+ files, involve data migrations, change an API contract, or will need to be explained to colleagues. The full cycle includes formalized requirements, design, decomposition into atomic steps, and tests as acceptance criteria.

**“Ad-hoc” mode** is for single-file fixes, typos, and tiny refactors. One sentence describing the problem → fix → tests → commit with a meaningful message.

The key skill is **recognizing the transition point**. If an ad-hoc fix has grown to three files or requires a migration, that is the moment to stop and switch to planned mode. The reverse is also true: writing a full spec for a one-line fix is overhead with no payoff.

The main trap in ad-hoc mode is commit messages. `fixes` and `bug fix` feel clear while you are typing them and become useless a week later. Even in a fast mode, one line of “what and why” (`fix: normalize backslash separators in export path matching`) pays for itself the first time you run `git bisect`.

---

## 10. Managing session length

Agentic IDEs create a feeling of flow—changes happen quickly, and the conversation does not stop. But after 2–3 hours of uninterrupted work, attention to diffs predictably drops. A classic symptom is a chain of five `fix:` commits in one evening, each repairing a bug introduced by the previous one.

Practical rules:

- **One work block** = 1–2 hours of focused effort on one feature. Take a break between blocks.
- **Stop signal**: the third consecutive fix to the same commit; the urge to write just `fixes`; the inability to explain the latest diff.
- **Action when stopping**: save the current state, write down “where I am and what comes next,” then return after a break and start by reviewing your own diff.

A cascade of fixes is not the result of “model stupidity.” It is a consequence of accepting the first commit without enough verification. If a second fix is needed within an hour, it is often more efficient to roll back and redo the first commit properly than to keep patching.

---

## 11. Solo development: when there is no reviewer

The advice to get “a second pair of eyes” breaks down when you are the only developer. In that case, the process has to compensate for the lack of external review.

**“The morning after.”** Do not merge a feature branch on the same day. Overnight, your brain reorganizes context; in the morning, you read your own diff like half-foreign code and notice what you missed in the flow.

**Automated tests as a behavior reviewer.** Without human review, property tests become a critical safety barrier. They cover scenarios you did not think of during implementation.

**LLM as an extra set of eyes (with caveats).** You can ask the model in a separate session to review the diff. But the model does not know your business context and may miss logical errors that formally compile. It is an extra signal, not a substitute.

**The two-hour rule.** Leave at least two hours between the last commit in a branch and merging into `main`. During that time, tests run, your head cools down, and `git diff main..HEAD` can be read as one coherent change.

---

## When an LLM is a poor primary tool

Honesty matters for team trust as well. A language model is a weak primary executor when you need:

- legally significant wording without a lawyer involved;
- safety-critical logic without formal verification;
- incident investigation without logs and reproducible facts.

In those situations, AI can still help as a **drafting tool or thinking partner**, but the decision and the process remain human responsibilities.

---

## Conclusion

Effective work with LLMs looks very little like a hunt for the “perfect prompt.” It is an engineering discipline: a clear goal, clarifying questions, architectural alignment, an environment where the result can be verified, and human responsibility for what lands in the repository and in production.

If you had to pass this experience to a team in one sentence, it would be this:

> **Dialogue and verification accelerate development; prompt magic without verification only accelerates mistakes.**

---

## A short cheat sheet of phrases

| Situation | Example |
|----------|--------|
| Checking understanding | “Am I understanding correctly: … because …, and we are not doing … because …?” |
| Task boundaries | “Do X. Do not touch Y or Z. First the plan, then the code.” |
| Alternatives | “Is there a simpler option? What is the long-term maintenance cost?” |
| Domain nuance | “Keep in mind the data is on a read-only NAS. How does that change the solution?” |
| API freshness | “Cross-check with the documentation for version N and our lockfile.” |
| Before coding | “Describe the architecture and risks. Code only after alignment.” |
| After the patch | “Run the tests and the linter. If anything fails, fix it and report back.” |

---

*Prepared from practical experience developing with agentic IDEs and language models. June 2026.*

*Related internal guide with checklists: [llm-development-practices.en.md](./llm-development-practices.en.md).*
