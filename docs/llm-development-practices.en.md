# Working with LLMs in Software Development: A Short Guide for the Team

An internal knowledge-sharing document. LLMs are not a replacement for developers; they are force multipliers when paired with a solid process: clear goals, clarifying questions, architecture alignment, durable project context, and mandatory verification of the result.

---

## How the process works (9 blocks)

| Block | Summary |
|------|------|
| **1. Dialogue** | Communicate clearly; do not hesitate to ask follow-up questions |
| **2. Critical thinking** | Evaluate solutions and the “cost of the question” |
| **3. Freshness** | Re-check library and API versions |
| **4. Architecture** | Align before making large changes |
| **5. Environment and project** | Prepare the repository for collaborative human–model work |
| **6. Verification** | Tests, diff review, and responsibility stay with the human |
| **7. Work modes** | Spec mode for features, ad-hoc for hotfixes—with an explicit threshold for switching |
| **8. Session management** | Know when to stop and how to avoid drowning in a cascade of fixes |
| **9. Solo work** | Compensate for the lack of a reviewer with process |

---

## Checklist before starting a task

- [ ] I stated the goal in **plain language**, but with clear boundaries (what to do / what not to touch)
- [ ] I named the constraints: data volume, deadlines, development platforms, documentation language, i18n
- [ ] I confirmed that **the assistant understood the task the same way I did** (rephrased it in its own words)
- [ ] I asked: **is there an alternative** and what is the “cost of the question” (complexity, lock-in, maintenance)
- [ ] I asked **my own domain-specific questions** (the model often does not know your nuances)
- [ ] For external dependencies, I checked **current versions and APIs** (docs, repository, search)
- [ ] I discussed **architecture** before a large diff and broke the task into steps
- [ ] The environment can **run checks** (build, tests, linter) in one or two commands
- [ ] Repeated project context is **captured in the repository** (rules, `AGENTS.md`, scripts), not only in chat
- [ ] The **risk level** is stated: draft / production / security-critical

## Checklist after the model responds

- [ ] I reviewed the **diff**, not just the text in chat
- [ ] I ran **tests and the linter** (or a manual scenario for UI)
- [ ] I checked for extra files, secrets, or accidental dependencies
- [ ] If something is unclear, I **asked for an explanation** before merging
- [ ] For a large change, I got a **second pair of eyes** from a colleague or a separate review pass

---

## Practices by block

### 1. Dialogue

- Use **natural language**—models are trained on human speech.
- “Natural” does not mean “vague”: add specifics wherever boundaries matter.
- Do not be shy: *“I didn’t follow that—explain how it works.”*
- Repeat questions until you align both on **understanding** and on the chosen approach.

### 2. Critical thinking

- Ask for justification: *why this library rather than another one?*
- Evaluate the “cost of the question”: migrations, performance, dependencies, long-term maintenance.
- Ask the questions that **matter to you**: data size, SLA, legacy constraints, company policies.

### 3. Freshness

- A model may confidently suggest an **outdated or non-existent API**.
- Explicitly ask it to cross-check documentation, the codebase, and current releases.
- Still **run the code**—reasoning in chat is not a substitute for execution.

### 4. Architecture

- Discuss structure **before** mass edits.
- Prefer several **small, agreed steps** over a single “do everything.”
- Capture long-lived decisions in a short note or ADR if the choice will stick around.

### 5. Environment and project

- Set up the environment for the **human + model pair**, not just for yourself:
  - shared commands (`task test`, `npm run lint`, `pytest`);
  - project rules stored in repository files;
  - access to the terminal, git, and the code structure.
- Fewer IDE extensions means less noise and fewer conflicts.
- The workstation is **dialogue + diff review + running checks**, not just a prompt window.
- When configuring tools, discuss cross-platform **development**, i18n, and the language and format of documentation with the model.
- Periodically **revisit your practices**: cloud models and tools change faster than it seems.

### 6. Verification and responsibility

| Human | Model |
|---------|--------|
| goal, priorities, “why” | drafts, options, routine work |
| constraints and risks | volume of code, refactoring |
| diff review, final “merge / do not merge” | codebase search, error-fix iterations |
| responsibility for production | — |

- Rule: **it is not accepted until checks are green** (or there is an explicitly documented exception).
- Do not paste secrets into chat; be cautious with new dependencies suggested by the model.

### When an LLM is a poor primary tool

- Legally sensitive wording without a lawyer.
- Safety-critical logic without formal verification.
- Incident investigation without logs and facts—you need a human and a process.

---

## Two work modes: spec vs. ad-hoc

In practice, working with LLMs falls into two fundamentally different modes. Both are legitimate—but they require different discipline.

### “Spec” mode (planned feature)

The full cycle: requirements → design → tasks → step-by-step implementation → property tests → commit with a conventional message.

**When to use it:**
- New functionality touching 3+ files
- Changes to architecture or the data schema
- Features that affect both backend and frontend
- Anything you will need to explain to colleagues a week from now

**Characteristics:**
- Formal acceptance criteria (SHALL/WHEN/THEN)
- Atomic tasks with checkpoints
- Property tests as an acceptance criterion
- A feature branch from `main`

### “Ad-hoc” mode (hotfix, experiment)

A fast fix without a formal spec: problem statement → fix → verification → commit.

**When it is acceptable:**
- A bug localized to one file or method
- A post-deploy fix (hotfix)
- An experiment that can be rolled back with a single `git revert`
- Cosmetic work: typos, import ordering, tiny refactors

**Minimal ad-hoc checklist:**
- [ ] I described the problem in one sentence (what is broken and where)
- [ ] I verified that the fix does not affect neighboring modules (diff < 50 lines)
- [ ] I ran tests / the build
- [ ] The commit message contains **what** and **why**, even if brief (`fix: normalize path separators in export — backslashes from Windows imports`)
- [ ] If the fix expanded to 3+ files, I stopped and switched to “Spec” mode

### The threshold for switching modes

| Signal | Ad-hoc is enough | You need a spec |
|---------|------------------|-----------------|
| Files touched | 1–2 | 3+ |
| DB migration involved | No | Yes |
| API contract affected | No | Yes |
| New dependencies required | No | Yes |
| You will need to explain it | No | Yes |
| Implementation time | < 30 min | > 30 min |

If you realize during ad-hoc work that you have opened a third file, that is your cue to stop, create a spec, and switch to planned mode.

---

## Session management: knowing when to stop

Long LLM sessions create an illusion of progress. After 2–3 hours of uninterrupted work, attention to diffs drops, and the threshold for “eh, looks fine” gets lower. A series of 5+ consecutive fixes in one evening is often a symptom that the first commit was accepted without enough verification.

### Rules for managing session length

**Signals that it is time to stop:**
- The third `fix:` commit in a row on the same feature
- Feeling tempted to write a commit message like `fixes` with no detail
- Discovering a bug that you yourself introduced 20 minutes ago
- Losing focus: you can no longer explain what the latest diff actually does
- A session lasting more than 3 hours without a break

**What to do:**
- Save the current state (WIP commit or stash)
- Write one sentence: “where I stopped, what comes next”
- Return after a break and start by reviewing your own diff for the session

**Planning work blocks:**
- One “spec block” = 1–2 hours of focused work
- Between blocks, take a break or switch context
- Daily limit: 2–3 blocks of planned LLM work, then asynchronous tasks or review

### Anti-pattern: “just one more fix”

The most common degradation pattern:
```
feat commit → bug discovered → fix → another bug → fix → another → fix → fix → fix
```

The reason: the first commit was accepted without enough verification (tests did not cover an edge case, the diff was reviewed too casually). Each next fix is debt created by skipped verification.

The remedy: if a second fix to the same commit is needed within an hour, stop, roll back to a stable state, find the root cause, and redo the first commit properly.

---

## Solo work: compensating for the lack of a reviewer

When there is no colleague for code review, you need to compensate for that with process.

### Strategies

**1. “The morning after”**
Do not merge a feature branch on the same day. Review your own PR the next morning with a fresh head. You will often spot things you missed in the flow.

**2. Delayed self-review**
After finishing a block of work, read `git diff main..HEAD` in full. Read it like someone else’s code: are the names clear, is there anything extra, do the changes agree with each other?

**3. LLM as a reviewer (with caveats)**
You can ask the model in a separate session to review the diff—but remember the limits:
- The model does not know your business context
- The model may miss logical errors that formally “compile”
- This is not a substitute for review, only an additional signal

**4. Property tests as a “behavior reviewer”**
If human review is not available, automated tests become critically important. Property tests (not just unit tests) cover scenarios you may not have thought about.

**5. Pre-merge solo checklist**
- [ ] More than 2 hours have passed since the last commit in the branch (the work has cooled off)
- [ ] I read `git diff main..HEAD` in full
- [ ] Tests are green
- [ ] Commit messages are understandable without chat context
- [ ] There are no files I “forgot to remove” (debug logs, TODO hacks, temporary dependencies)

---

## Example prompt phrases

**Checking understanding**

> Am I understanding you correctly: you are proposing to cache X in Redis because of Y, and we are postponing the in-memory option because of Z?

**Task boundaries**

> Add an endpoint for exporting notes. Do not change the database schema and do not touch the frontend. First give me a 5-point plan, then the code.

**Alternatives and trade-offs**

> Compare libraries A and B for our use case: 500 GB of data, nightly batch, Python 3.13. Which one will be easier to maintain in two years?

**Domain nuance**

> Keep in mind that the source files are on a NAS, the path is read-only, and the index lives in PostgreSQL. How does that change the plan?

**API freshness**

> Check the signatures against the current FastAPI 0.115 docs and our `pyproject.toml`. Do not use deprecated parameters.

**Architecture before code**

> Before writing code, describe the components, data flows, and failure points. I want alignment first—then step-by-step implementation.

**Explanation**

> Explain how this patch works as if you were speaking to a colleague who has not seen the context. Where is the main regression risk?

**Verification**

> After the changes, run the tests and the linter. If something fails, fix it and briefly report what broke.

**Risk level**

> This is a draft for an experiment: simplifications are fine and minimal tests are enough. / This changes authorization: I need a conservative plan and full test coverage.

---

## Anti-patterns

| Anti-pattern | Why it is bad | What to do instead |
|-------------|--------------|-------------------|
| One huge prompt: “redo everything” | Regressions, unaligned architecture | Break it into steps with checkpoints |
| Trusting a pretty explanation without running anything | The model is persuasive even when wrong | Tests, build, manual scenario |
| Looking only at the chat, not the diff | Neighboring code breaks | Review every changed file |
| Bureaucratic wording and empty abstractions | The model cannot infer priorities | Use plain language + explicit constraints |
| Keeping all context only in your head | The model will not guess terabytes and a NAS | Ask your own questions and capture them in the repo |
| A zoo of IDE extensions | Noise, conflicts, mismatched context | Keep it minimal; focus on the project and checks |
| “The model works, I relax” | Production responsibility remains with the human | Dialogue + direction + verification |
| Blindly copying dependencies | Supply-chain risk and unnecessary weight | Ask explicitly about alternatives and risks |
| Secrets in prompts | Key leakage | `.env`, environment variables, company policy |
| Arguing with the model without facts | Wasted time | Logs, tests, docs—then clarify |
| Commit message `fixes` with no context | Impossible to understand a week later; hard to revert one specific change | Even in ad-hoc mode: `fix: what and why` on one line |
| A marathon of 20+ commits in a day | Attention degrades → fix cascade → technical debt | 2–3 focused blocks of 1–2 hours with breaks |
| Ad-hoc work across 5+ files | This is no longer a hotfix but an unplanned feature without a spec | Stop, create a spec, switch to planned mode |
| Merging a branch the day it was created (solo) | No “cold” verification pass | Delay the PR until “the morning after” |

---

## Minimal working cycle (one task)

### “Spec” mode (planned feature)

```
1. Goal + boundaries + constraints → requirements.md
2. Design / architecture → design.md → alignment
3. Decomposition → tasks.md (atomic steps + checkpoints)
4. Implement one task at a time
5. Diff + tests + linter after each step
6. Checkpoint: everything green → next block
7. Something unclear → “explain” → rollback / adjustment if needed
8. Commit with a conventional message
```

### “Ad-hoc” mode (hotfix)

```
1. Problem in one sentence (what, where, how to reproduce)
2. Fix → diff < 50 lines, 1–2 files
3. Tests are green
4. Commit: fix: what and why
5. If it expands to 3+ files → STOP → create a spec
```

---

## One sentence for leadership

**Effective work with LLMs is discipline in dialogue and verification, not prompt magic:** the better you frame the goal, catch blind spots, and run checks, the fewer rewrites you will need and the safer your development acceleration will be.

---

*Version: 2026-06-21. Source: internal team experience; expanded with practices for agentic IDEs, mandatory output verification, and analysis of real behavior (git history, spec metadata).*

*Version for publication (article): [llm-development-practices-article.en.md](./llm-development-practices-article.en.md).*
