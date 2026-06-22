# Working with LLMs in Software Development: A Short Team Guide

An internal knowledge-sharing document. LLMs don't replace developers—they amplify them, but only when the process is solid: clear goals, clarifying questions, architectural alignment, durable project context, and mandatory verification of output.

---

## How the process is organized (9 blocks)

| Block | Key idea |
|-------|----------|
| **1. Dialogue** | Communicate clearly; don't hesitate to ask follow-ups |
| **2. Critical thinking** | Challenge solutions; weigh the "cost of the question" |
| **3. Freshness** | Double-check library and API versions |
| **4. Architecture** | Align before making large changes |
| **5. Environment & project** | Set up the repository for human + model collaboration |
| **6. Verification** | Tests, diff review, and accountability stay with the human |
| **7. Work modes** | Spec mode for features, ad-hoc for hotfixes—with an explicit switch threshold |
| **8. Session management** | Know when to stop; avoid drowning in a fix cascade |
| **9. Solo work** | Compensate for the missing reviewer through process |

---

## Checklist before starting a task

- [ ] Stated the goal in **plain language** with clear boundaries (what to do / what not to touch)
- [ ] Named constraints: data volumes, deadlines, target platforms, documentation language, i18n
- [ ] Confirmed the **assistant understood the task the same way I did** (rephrased in its own words)
- [ ] Asked: **is there an alternative**, and what's the "cost of the question" (complexity, lock-in, maintenance)
- [ ] Asked **my own domain-specific questions** (the model often doesn't know your specifics)
- [ ] For external dependencies: verified **current versions and APIs** (docs, repo, search)
- [ ] Discussed **architecture** before a large diff; broke the task into steps
- [ ] The environment can **run checks** (build, tests, linter) in one or two commands
- [ ] Recurring project context is **captured in the repository** (rules, `AGENTS.md`, scripts)—not only in chat
- [ ] Stated the **risk level**: draft / production / security-critical

## Checklist after the model responds

- [ ] Read the **diff**, not just the text in chat
- [ ] Ran **tests and linter** (or a manual scenario for UI)
- [ ] Checked for stray files, leaked secrets, or accidental dependencies
- [ ] If anything is unclear, **asked for an explanation** before merging
- [ ] For a large change—got a **second pair of eyes** from a colleague or a separate review pass

---

## Practices by block

### 1. Dialogue

- Use **natural language**—models are trained on human speech.
- "Natural" doesn't mean "vague": add specifics wherever boundaries matter.
- Don't be shy: *"I didn't follow that—explain how it works."*
- Keep asking until you converge on both **understanding** and the chosen approach.

### 2. Critical thinking

- Demand justification: *why this library and not another?*
- Weigh the "cost of the question": migrations, performance, dependency footprint, long-term maintenance.
- Ask the questions that **matter to you**: data volume, SLA, legacy constraints, company policies.

### 3. Freshness

- A model may confidently suggest an **outdated or nonexistent API**.
- Explicitly ask it to cross-check documentation, the codebase, and current releases.
- **Run the code** regardless—reasoning in chat doesn't replace execution.

### 4. Architecture

- Discuss structure **before** mass edits.
- Prefer several **small, agreed-upon steps** over a single "do everything."
- Record long-lived decisions in a short note or ADR.

### 5. Environment & project

- Configure the environment for the **human + model pair**, not just for yourself:
  - shared commands (`task test`, `npm run lint`, `pytest`);
  - project rules stored in repo files;
  - terminal, git, and code-structure access.
- Fewer IDE extensions → less noise, fewer conflicts.
- The workstation is **dialogue + diff review + running checks**—not just a prompt window.
- When setting up tooling, discuss cross-platform **development** needs, i18n, and documentation language/format with the model.
- **Revisit your practices** periodically: cloud models and tools change faster than you'd expect.

### 6. Verification and accountability

| Human | Model |
|-------|-------|
| Goal, priorities, "why" | Drafts, options, routine work |
| Constraints and risks | Code volume, refactoring |
| Diff review, final merge/reject call | Codebase search, error-fix iterations |
| Production accountability | — |

- Rule: **not accepted until checks are green** (or there's an explicitly documented exception).
- Don't paste secrets into chat; be cautious with dependencies the model suggests.

### When an LLM is the wrong primary tool

- Legally binding wording without a lawyer.
- Safety-critical logic without formal verification.
- Incident investigation without logs and reproducible facts—you need a human and a process.

---

## Two work modes: spec vs. ad-hoc

Day-to-day work with LLMs splits into two fundamentally different modes. Both are legitimate—but they demand different discipline.

### Spec mode (planned feature)

Full cycle: requirements → design → tasks → step-by-step implementation → property tests → commit with a conventional message.

**When to use:**
- New functionality touching 3+ files
- Architecture or data-schema changes
- Features spanning both backend and frontend
- Anything you'll need to explain to colleagues a week later

**Characteristics:**
- Formal acceptance criteria (SHALL / WHEN / THEN)
- Atomic tasks with checkpoints
- Property tests as the acceptance gate
- Feature branch off `main`

### Ad-hoc mode (hotfix, experiment)

A fast fix without a formal spec: problem statement → fix → verification → commit.

**When it's appropriate:**
- A bug confined to a single file or method
- A post-deploy hotfix
- An experiment reversible with a single `git revert`
- Cosmetic work: typos, import ordering, minor refactors

**Minimal ad-hoc checklist:**
- [ ] Described the problem in one sentence (what's broken, where)
- [ ] Confirmed the fix doesn't spill into neighboring modules (diff < 50 lines)
- [ ] Ran tests / build
- [ ] Commit message states **what** and **why**, even if brief (`fix: normalize path separators in export — backslashes from Windows imports`)
- [ ] If the fix grew to 3+ files—stopped and switched to Spec mode

### Threshold for switching modes

| Signal | Ad-hoc is fine | You need a spec |
|--------|----------------|-----------------|
| Files touched | 1–2 | 3+ |
| DB migration involved | No | Yes |
| API contract affected | No | Yes |
| New dependencies required | No | Yes |
| Will need explaining later | No | Yes |
| Implementation time | < 30 min | > 30 min |

If you notice you've opened a third file during ad-hoc work, that's your cue to stop, create a spec, and switch to planned mode.

---

## Session management: knowing when to stop

Long LLM sessions breed an illusion of progress. After 2–3 hours of uninterrupted work, attention to diffs drops and the bar for "eh, looks fine" gets dangerously low. A run of 5+ consecutive fixes in one evening usually means the first commit was accepted without enough scrutiny.

### Guidelines for session length

**Signals it's time to stop:**
- Third `fix:` commit in a row on the same feature
- Temptation to write a commit message like `fixes` with no further detail
- Finding a bug you introduced 20 minutes ago
- Loss of focus: you can't explain what the latest diff actually does
- Session running longer than 3 hours without a break

**What to do:**
- Save current state (WIP commit or stash)
- Write one sentence: "where I stopped, what's next"
- Return after a break and start by reviewing your own diff from the session

**Planning work blocks:**
- One "spec block" = 1–2 hours of focused work
- Break or context-switch between blocks
- Daily cap: 2–3 blocks of planned LLM work, then async tasks or review

### Anti-pattern: "just one more fix"

The most common degradation scenario:
```
feat commit → bug found → fix → another bug → fix → another → fix → fix → fix
```

Root cause: the first commit was accepted without adequate verification (tests didn't cover an edge case, diff was skimmed). Each subsequent fix is debt from the skipped check.

Remedy: if a second fix to the same commit is needed within an hour—stop, roll back to the last stable state, identify the root cause, and redo the original commit properly.

---

## Solo work: compensating for the missing reviewer

When there's no colleague for code review, process has to fill the gap.

### Strategies

**1. "The morning after"**
Don't merge a feature branch the same day you wrote it. The next morning, review your own PR with fresh eyes. You'll almost always spot things you missed in the flow.

**2. Delayed self-review**
After finishing a work block, read `git diff main..HEAD` end to end. Read it as if it were someone else's code: are the names clear, is anything superfluous, do the changes form a coherent whole?

**3. LLM as reviewer (with caveats)**
You can ask the model in a separate session to review the diff—but keep expectations realistic:
- The model doesn't know your business context
- It may miss logical errors that happen to compile
- Treat it as an extra signal, not a substitute for review

**4. Property tests as a "behavior reviewer"**
Without human review, automated tests become critically important. Property tests (not just unit tests) exercise scenarios you may not have considered.

**5. Solo pre-merge checklist**
- [ ] At least 2 hours have passed since the last commit on this branch (cooled off)
- [ ] Read `git diff main..HEAD` end to end
- [ ] Tests are green
- [ ] Commit messages make sense without chat context
- [ ] No leftover files (debug logs, TODO hacks, temp dependencies)

---

## Example prompt phrases

**Checking understanding**

> Let me confirm: you're proposing to cache X in Redis because of Y, and we're deferring the in-memory option because of Z?

**Task boundaries**

> Add an endpoint for exporting notes. Don't change the DB schema and don't touch the frontend. Plan in five points first, then code.

**Alternatives and trade-offs**

> Compare libraries A and B for our scenario: 500 GB of data, nightly batch, Python 3.13. Which will be easier to maintain two years from now?

**Domain nuance**

> Bear in mind the source files are on a NAS, the path is read-only, and the index lives in PostgreSQL. How does that change the plan?

**API freshness**

> Verify the signatures against the current FastAPI 0.115 docs and our `pyproject.toml`. Don't use deprecated parameters.

**Architecture before code**

> Before writing code: describe the components, data flows, and failure points. I want to align first—then step-by-step implementation.

**Explanation**

> Walk me through how this patch works, as if explaining to a colleague who hasn't seen the context. Where's the main regression risk?

**Verification**

> After the changes, run tests and linter. If anything fails—fix it and briefly report what broke.

**Risk level**

> This is a rough draft for an experiment: simplifications are fine, minimal tests are enough. / This touches authorization: I need a conservative plan and full test coverage.

---

## Anti-patterns

| Anti-pattern | Why it's harmful | What to do instead |
|--------------|-----------------|-------------------|
| One giant prompt: "redo everything" | Regressions, incoherent architecture | Break into steps with checkpoints |
| Trusting a polished explanation without running anything | Models are persuasive even when wrong | Tests, build, manual scenario |
| Reading only the chat, not the diff | Adjacent code breaks silently | Review every changed file |
| Bureaucratic wording and empty abstractions | Model can't infer priorities | Plain language + explicit constraints |
| Keeping all context in your head | Model can't guess terabytes and a NAS | Ask your own questions; capture answers in the repo |
| A zoo of IDE extensions | Noise, conflicts, inconsistent context | Keep it minimal; focus on the project and checks |
| "The model works, I relax" | Production accountability stays with the human | Dialogue + direction + verification |
| Blindly copying suggested dependencies | Supply-chain risk and bloat | Explicitly ask about alternatives and risks |
| Secrets in prompts | Credential leakage | `.env`, env vars, company policy |
| Arguing with the model without evidence | Wasted time | Logs, tests, docs—then refine |
| Commit message `fixes` with no context | Impossible to understand a week later; hard to revert selectively | Even in ad-hoc: `fix: what and why` in one line |
| 20+ commit marathon in a day | Attention degrades → fix cascade → tech debt | 2–3 blocks of 1–2 hours with breaks |
| Ad-hoc across 5+ files | That's not a hotfix—it's an unplanned feature without a spec | Stop, create a spec, switch to planned mode |
| Merging a branch the day it was created (solo) | No "cold" verification pass | Defer the PR to "the morning after" |

---

## Minimal working cycle (one task)

### Spec mode (planned feature)

```
1. Goal + boundaries + constraints → requirements.md
2. Design / architecture → design.md → alignment
3. Decomposition → tasks.md (atomic steps + checkpoints)
4. Implement one task at a time
5. Diff + tests + linter after each step
6. Checkpoint: all green → next block
7. Something unclear → "explain" → roll back / revise if needed
8. Commit with a conventional message
```

### Ad-hoc mode (hotfix)

```
1. Problem in one sentence (what, where, how to reproduce)
2. Fix → diff < 50 lines, 1–2 files
3. Tests green
4. Commit: fix: what and why
5. If it grows to 3+ files → STOP → create a spec
```

---

## One sentence for leadership

**Effective LLM-assisted development is a discipline of dialogue and verification, not prompt magic:** the better you frame the goal, surface blind spots, and run checks, the fewer rewrites you'll need and the safer your development velocity becomes.

---

*Version: 2026-06-21. Source: internal team experience; expanded with practices for agentic IDEs, mandatory output verification, and analysis of real-world behavior (git history, spec metadata).*

*Publication version (article): [llm-development-practices-article.en.md](./llm-development-practices-article.en.md).*
