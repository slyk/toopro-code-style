# AI-Driven Feature Development Workflow

## Overview

This document defines the 3-phase methodology for developing new features using AI agents. Every new feature MUST follow this workflow to ensure architectural consistency, traceability, and quality.

**This workflow is language-agnostic.** It works with any tech stack — TypeScript/NestJS, Angular, React, PHP, Python, etc. Templates adapt to the project's specific technologies.

## Folder Structure

```
_ai/
  architecture.md              # System architecture (keep updated after significant changes)
  decisions/                   # Architecture Decision Records (ADRs)
    ADR-{N}-{feature-slug}.md  # One per feature decision
  implementations/             # Implementation tracking by feature
    {feature-slug}/            # One folder per feature
      implementation-plan.md   # Scope, acceptance criteria, file changes
      backend-implementation.md    # (optional) Backend: API, DB, services
      frontend-implementation.md   # (optional) Frontend: components, state, routing
      shared-implementation.md     # (optional) Shared: libs, DTOs, types
      progress.md              # Task checklist with status tracking
  knowledge-base/              # Reusable patterns & guides about existing subsystems
    {topic}.md                 # Reference docs written by developers
```

### Setup

If the `_ai/` folder doesn't exist yet, create it at the **project root** (for Nx monorepos — at the workspace root):

```bash
mkdir -p _ai/decisions _ai/implementations _ai/knowledge-base
```

Create `_ai/architecture.md` if the project doesn't have one yet. Use `templates/architecture-template.md` as a starting point.

---

## Phase 1: DECIDE (Architecture Decision Record)

**Output:** `_ai/decisions/ADR-{N}-{feature-slug}.md`

### When to trigger

- User requests a new feature or significant change
- User says "new feature", "add feature", "implement X", "build X", or similar
- Any change that affects multiple modules/services or introduces new patterns

### Steps

1. **Analyze requirements** — Clarify scope, constraints, affected areas
2. **Check knowledge-base** — Read `_ai/knowledge-base/` for relevant patterns and existing subsystem documentation
3. **Research existing code** — Read `_ai/architecture.md`, project's `CLAUDE.md`/`AGENTS.md`, and relevant source code
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
2. Generate these documents (use templates from `templates/`):

| Document | Required? | When to include |
|----------|-----------|-----------------|
| `implementation-plan.md` | **Always** | Main plan: scope, acceptance criteria, file list, build order |
| `backend-implementation.md` | Optional | Feature has backend work: API endpoints, DB proposals, services, message queues |
| `frontend-implementation.md` | Optional | Feature has frontend work: components, state management, routing, UI |
| `shared-implementation.md` | Optional | Feature affects shared code: libs, DTOs, types, utilities |
| `progress.md` | **Always** | Task checklist — auto-generated from the plan |

3. Each implementation doc must list **exact files to create/modify** with descriptions of changes
4. Implementation plan must define a clear **build order** (what to implement first)

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

Database changes are **proposals for the DB engineer**, not direct migrations. In the backend implementation spec:
- Describe the required data structure changes (new fields, collections, relations)
- Explain why each change is needed
- Note any indexes or constraints required
- The DB engineer will implement these in the database layer (Directus, migrations, etc.)

### Gate: User MUST approve the plan before proceeding to Phase 3.

---

## Phase 3: BUILD (Execute & Track)

**Tracking:** `_ai/implementations/{feature-slug}/progress.md`

### Steps

1. **Read implementation specs** before coding — agents MUST read their relevant spec
2. **Follow the build order** defined in the implementation plan
3. **Update `progress.md`** after completing each task:
   - `[ ]` — Not started
   - `[x]` — Completed
   - `[!]` — Blocked (with reason in Blockers table)
4. **Follow project code style** — check `CLAUDE.md`/`AGENTS.md` and any code-style skill
5. **After all tasks complete:**
   - Update `_ai/architecture.md` if architecture changed
   - Note any new patterns or gotchas that future features should know about

### Build Principles

- Implement backend changes before frontend (data layer first)
- Shared libs/types before consumers
- Test each layer before moving to the next
- If blocked, mark the task with `[!]` and move to the next unblocked task

---

## Knowledge Base

The `_ai/knowledge-base/` folder contains documentation about existing subsystems, patterns, and integration guides written by developers.

### Reading (during DECIDE and PLAN phases)

Before starting any feature, **always check the knowledge base** for:
- Patterns similar to what you're building
- Documentation about subsystems you'll interact with
- Integration guides for external services
- Gotchas and lessons learned from previous features

### Writing

Knowledge base entries are typically written by developers, not by AI agents during the feature workflow. However, if you discover a significant pattern or integration detail during BUILD that isn't documented elsewhere, suggest to the user that it should be added.

---

## Architecture Documentation

### `_ai/architecture.md`

A living document that describes the system's current architecture. Keep it updated after significant changes.

**When to update:**
- New service or module added
- Communication pattern changed
- Data model significantly altered
- New external integration added

**What to include:**
- High-level system overview
- Service/module responsibilities
- Communication patterns (HTTP, message queues, events)
- Data flow diagrams (text-based)
- Key design decisions and constraints

If the project has multiple distinct layers (e.g., separate backend and frontend architectures), you may split into `architecture_backend.md` and `architecture_frontend.md`. For single-stack projects, one `architecture.md` is sufficient.

---

## Quick Reference

| Phase | Output | Gate |
|-------|--------|------|
| DECIDE | `_ai/decisions/ADR-{N}-{slug}.md` | User approval |
| PLAN | `_ai/implementations/{slug}/` | User approval |
| BUILD | Code + `progress.md` updates | Tests pass |

### Status Legend for `progress.md`

| Symbol | Meaning |
|--------|---------|
| `[ ]` | Not started |
| `[x]` | Completed |
| `[!]` | Blocked |
