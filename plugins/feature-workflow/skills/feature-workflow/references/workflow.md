# AI-Driven Feature Development Workflow

## Overview

This document defines the 4-phase methodology for developing significant features using AI agents: DECIDE, PLAN, BUILD, CLOSE. It gives architectural consistency and traceability where they pay off. It is not meant for every change — see "When to trigger".

**This workflow is language-agnostic.** It works with any tech stack — TypeScript/NestJS, Angular, React, PHP, Python, etc. Templates adapt to the project's specific technologies.

## Folder Structure

```
_ai/
  decisions/                   # Architecture Decision Records (ADRs)
    ADR-{N}-{feature-slug}.md  # One per feature decision: why this design
  implementations/             # Implementation tracking by feature
    {feature-slug}/            # One folder per feature
      implementation-plan.md   # The plan: scope, criteria, changes, data, verification
      progress.md              # Task checklist with status tracking
      backend-implementation.md    # (only for large features) detail split out of the plan
      frontend-implementation.md   # (only for large features)
      shared-implementation.md     # (only for large features)
  knowledge-base/              # How each subsystem works NOW: invariants, gotchas, reasons
    {topic}.md                 # Written or updated in the CLOSE phase of every feature
  architecture.md              # (optional) system overview, if the project keeps one
```

### Setup

If the `_ai/` folder doesn't exist yet, create it at the **project root** (for Nx monorepos — at the workspace root):

```bash
mkdir -p _ai/decisions _ai/implementations _ai/knowledge-base
```

`_ai/architecture.md` is optional. Many projects already describe their architecture in `CLAUDE.md`/`AGENTS.md` and the knowledge base; in that case do not create a second description. Create it (from `templates/architecture-template.md`) only when the user asks for one.

### What each document is for

Each fact is written in one document. Others link to it instead of repeating it.

| Document | Holds | Does not hold |
|---|---|---|
| ADR | The problem, requirements, options, the decision and why | File lists, field lists, task lists |
| `implementation-plan.md` | Everything needed to build: changes by area, data changes, contracts, build order, verification | The reasoning behind the decision (link to the ADR) |
| `progress.md` | Task checklist and blockers | Descriptions of the tasks (link to the plan) |
| Knowledge base | How the subsystem works after the feature | History, options that were rejected |

---

## Phase 1: DECIDE (Architecture Decision Record)

**Output:** `_ai/decisions/ADR-{N}-{feature-slug}.md`

### When to trigger

Start the workflow when the change does at least one of these:

- alters a data schema (new fields, collections, relations, indexes)
- touches money, balances or other values that must stay consistent
- changes a contract between parts of the system (message, queue, API, a type shared with another codebase)
- spans more than one service or codebase, or introduces a new pattern others will copy
- the user asks for an ADR, an architecture decision or an implementation plan

Do **not** start it for a bug fix, a local refactor, or a change inside one module that changes no contract. Those are done directly and verified; only the knowledge-base document is updated if the behaviour it describes changed. When unsure, ask the user once.

### Steps

1. **Analyze requirements** — Clarify scope, constraints, affected areas
2. **Check knowledge-base** — Read `_ai/knowledge-base/` for relevant patterns and existing subsystem documentation
3. **Research existing code** — Read the project's `CLAUDE.md`/`AGENTS.md`, `_ai/architecture.md` if it exists, and the relevant source code
4. **Propose 2-3 approaches** with trade-offs (pros/cons for each)
5. **Create ADR** using `templates/adr-template.md`
6. **Present to user** for review and approval

### ADR Numbering

Sequential. Check existing files in `_ai/decisions/` and increment.

### Gate: User MUST approve the ADR before proceeding to Phase 2.

---

## Phase 2: PLAN (Implementation Specification)

**Output:** `_ai/implementations/{feature-slug}/` folder

### Steps

1. Create the feature folder: `_ai/implementations/{feature-slug}/`
2. Write **one** `implementation-plan.md` (from `templates/implementation-plan-template.md`) and `progress.md` (from `templates/progress-template.md`). By default these two files are the whole plan.
3. Split detail into `backend-implementation.md`, `frontend-implementation.md` or `shared-implementation.md` **only** when one of these holds:
   - different agents or people will build the parts and each should read only its own part
   - the plan would otherwise grow past roughly 300 lines

   A split spec holds the detail of its area; the plan then keeps a one-line summary and a link for that area. Never write the same change in both.
4. The plan defines a clear **build order** (what to implement first) and a **verification** section (how each acceptance criterion will be checked).

### How to reference code

Refer to code by **file path plus class, method or function name**: `cashback.spend.service.ts` → `CashbackSpendService.spendCoin()`. Do not write line numbers (`file.ts:247`) in an ADR, plan or knowledge-base document: they are wrong after the next edit, and a reader then trusts a pointer to the wrong code. Describe the change to a symbol in words; do not paste large code blocks that will be rewritten during BUILD.

### Nx Monorepo Considerations

When working in an Nx monorepo:

1. **Check `CLAUDE.md`/`AGENTS.md`** for the workspace's lib structure and conventions
2. **Determine affected projects** — which apps and libs are touched by this feature
3. **Decide on lib placement:**
   - If the project docs describe where new libs should go, follow that guidance
   - If the feature clearly belongs in an existing lib, use it
   - If a new lib might be needed, **ask the user** before proposing one in the plan
   - Consider: is this shared across apps (→ shared lib) or app-specific (→ app module)?
4. **Use `@workspace/*` path aliases** as defined in `tsconfig.base.json`
5. **Note affected Nx projects** in the implementation plan for targeted builds/tests

### Database Changes

Database changes are **proposals for the DB engineer**, not direct migrations. They are written in one place: the "Database Changes" section of `implementation-plan.md`. The ADR may say that the data model changes and why; the field list lives only in the plan.

- Describe the required data structure changes (new fields, collections, relations)
- Explain why each change is needed
- Note any indexes or constraints required
- The DB engineer will implement these in the database layer (Directus, migrations, etc.)

### Gate: User MUST approve the plan before proceeding to Phase 3.

---

## Phase 3: BUILD (Execute & Track)

**Tracking:** `_ai/implementations/{feature-slug}/progress.md`

### Steps

1. **Read the plan** (and your split spec, if there is one) before coding
2. **Follow the build order** defined in the implementation plan
3. **Update `progress.md`** after completing each task:
   - `[ ]` — Not started
   - `[x]` — Completed
   - `[!]` — Blocked (with reason in Blockers table)
4. **Follow project code style** — check `CLAUDE.md`/`AGENTS.md` and any code-style skill
5. **Run the verification** from the plan and record the result in `progress.md` (see the gate below)
6. **After all tasks complete**, go to Phase 4. BUILD being finished does not finish the feature.

### Build Principles

- Implement backend changes before frontend (data layer first)
- Shared libs/types before consumers
- Verify each layer before moving to the next
- If blocked, mark the task with `[!]` and move to the next unblocked task
- If BUILD shows the plan was wrong, change the plan (and tell the user) instead of silently building something else

### Gate: the plan's verification was run and its result is recorded.

"Verification" is whatever the plan's Verification section lists: automated tests where the project has them, otherwise a build, a lint run, a manual scenario, or a check the user performs. For each item `progress.md` records what was run and what happened. An item that could not be run (no test setup, no access, needs a deploy) is recorded as **not verified** with the reason — never as passed.

---

## Phase 4: CLOSE (Make the record true)

**Output:** correct statuses and `_ai/knowledge-base/{topic}.md`

The ADR, plan and specs describe an intention at one moment. After BUILD nobody reads them to learn how the system works, so the lasting result of a feature is its knowledge-base document and statuses that can be trusted.

### Steps

1. **Knowledge base** — write or update `_ai/knowledge-base/{topic}.md` for the subsystem that changed (see "Knowledge Base" below). One document per subsystem, not per feature: a second feature in the same area updates the existing document.
2. **ADR status** — set `**Status:** Implemented ({YYYY-MM-DD})`. Tick the requirements that were met; for any that was dropped or changed during BUILD, say so in one line. If the feature was abandoned, set `Rejected` or `Superseded by ADR-{N}` with the reason.
3. **`progress.md`** — set `**Status:** Complete`, and make every box true: `[x]` done, or removed with a note if the task was dropped. No `[ ]` may remain in a closed feature.
4. **Implementation plan** — set `**Status:** Complete`.
5. **Architecture docs** — update `_ai/architecture.md` if the project keeps one and the architecture changed.
6. **Pointers** — if the project's `CLAUDE.md`/`AGENTS.md` lists knowledge-base documents, add the new one there.
7. **Report to the user** what was built, what was verified and how, what was not verified, and what remains for a human (deploy, database changes, other codebases).

### Gate: the user confirms the feature is done. Until then the ADR stays `Approved` and `progress.md` stays `In Progress`.

---

## Knowledge Base

The `_ai/knowledge-base/` folder describes how existing subsystems work **now**. It is the first thing to read before changing an area and the required output of the CLOSE phase.

### Reading (during DECIDE and PLAN phases)

Before starting any feature, **always check the knowledge base** for:
- Patterns similar to what you're building
- Documentation about subsystems you'll interact with
- Integration guides for external services
- Gotchas and lessons learned from previous features

### Writing (CLOSE phase — required)

Every feature that goes through this workflow ends with a knowledge-base document written or updated by the agent that built it, and reviewed by the user. Write it for someone who will change this subsystem next and has not read the ADR:

- **How it works now** — the model in a few lines (what is the source of truth, what is derived, the main flow). Describe the present state; leave out the history of how it got there.
- **Invariants** — what must stay true, and what breaks if it doesn't.
- **Gotchas** — things that look wrong but are intended, legacy formats, traps that cost time during BUILD.
- **Reasons** — one line of "why" next to each non-obvious rule.
- **Cross-codebase duplicates** — logic that must stay identical in another repository or language.
- **How to verify** — the test or command that covers the area.
- **Related** — links to the ADR and the implementation folder.

Keep it short (one or two screens). Refer to classes and methods by name, not by line number. When a later change makes a statement false, fix the statement in the same change.

---

## Architecture Documentation (optional)

`_ai/architecture.md` is a living overview of the system. Keep one only if the project wants it and has no equivalent elsewhere; a project whose `CLAUDE.md`/`AGENTS.md` and knowledge base already cover this should not add a third description.

If the project keeps it, update it in the CLOSE phase when:
- a new service or module was added
- a communication pattern changed
- the data model was significantly altered
- a new external integration was added

It holds: a high-level overview, service/module responsibilities, communication patterns (HTTP, message queues, events), text-based data-flow diagrams, key design decisions and constraints. Projects with distinct layers may split it into `architecture_backend.md` and `architecture_frontend.md`.

---

## Quick Reference

| Phase | Output | Gate |
|-------|--------|------|
| DECIDE | `_ai/decisions/ADR-{N}-{slug}.md` | User approval |
| PLAN | `_ai/implementations/{slug}/` (plan + progress) | User approval |
| BUILD | Code + `progress.md` updates | Plan's verification run and recorded |
| CLOSE | True statuses + `_ai/knowledge-base/{topic}.md` | User confirms done |

### Status Legend for `progress.md`

| Symbol | Meaning |
|--------|---------|
| `[ ]` | Not started |
| `[x]` | Completed |
| `[!]` | Blocked |
