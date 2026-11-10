---
name: feature-workflow
description: >
  Feature development workflow with written decisions: DECIDE (ADR), PLAN (implementation specs),
  BUILD (execute and track), CLOSE (true statuses and a knowledge-base document). Use it for a change
  that alters a data schema, touches money or balances, changes a message or API contract, or spans
  more than one service or codebase, and whenever the user asks for an ADR, an architecture decision,
  an implementation plan or the feature workflow. Small or local changes do not need it.
---

# Feature Development Workflow

This skill defines the **4-phase methodology** for developing significant features using AI agents.

## When to Use It

Use the workflow when the change does at least one of these:

- alters a data schema (new fields, collections, relations, indexes)
- touches money, balances or other values that must stay consistent
- changes a contract between parts of the system (message, queue, API, shared type used by another codebase)
- spans more than one service or codebase
- or the user asks for an ADR or a plan

Anything smaller (a bug fix, a local refactor, a change inside one module with no contract change) is
done directly: make the change, verify it, and update the knowledge-base document if behaviour it
describes has changed. If you are unsure which side a request falls on, ask the user once.

**Read the full workflow guide:** `references/workflow.md`

## Quick Reference

| Phase | Output | Gate |
|-------|--------|------|
| **DECIDE** | `_ai/decisions/ADR-{N}-{slug}.md` | User approval required |
| **PLAN** | `_ai/implementations/{slug}/` (one plan + progress) | User approval required |
| **BUILD** | Code + `progress.md` updates | The plan's verification was run and recorded |
| **CLOSE** | True statuses + `_ai/knowledge-base/{topic}.md` | User confirms the feature is done |

## When Triggered

1. **Read `references/workflow.md`** for the complete process
2. **Check `_ai/decisions/`** for existing ADRs (to determine next ADR number)
3. **Check `_ai/knowledge-base/`** for relevant patterns and guides about existing subsystems
4. **Check project's `CLAUDE.md` or `AGENTS.md`** for architecture info, lib structure, and conventions (and `_ai/architecture.md` only if the project has one)
5. **Follow the 4 phases** with user approval gates between DECIDE, PLAN and BUILD

## Templates

All templates are in `references/templates/`:

| Template | When to use |
|----------|-------------|
| `adr-template.md` | Phase 1: the decision record — why, not what files |
| `implementation-plan-template.md` | Phase 2: the whole plan — changes, data, contracts, build order, verification |
| `progress-template.md` | Phase 2: task checklist, verification results, close checklist |
| `backend-implementation-template.md` | Phase 2, optional: only when the plan is split |
| `frontend-implementation-template.md` | Phase 2, optional: only when the plan is split |
| `shared-implementation-template.md` | Phase 2, optional: only when the plan is split |
| `architecture-template.md` | Optional: only if the project keeps `_ai/architecture.md` |

## Critical Rules

- **Don't start coding a workflow feature without an approved plan.** The plan is the contract.
- **Update `progress.md`** as you complete tasks during BUILD.
- **Check the knowledge base before starting** — it may describe the subsystem you are about to change.
- **Agents read the plan** (and their split spec, if any) before writing code.
- **Write each fact once.** The ADR holds the reasoning, the plan holds the changes and data fields, progress holds the checklist. Link instead of repeating.
- **Reference code by class or method name, never by line number** — line numbers are wrong after the next edit.
- **Never report verification you did not run.** If something could not be checked, record it as not verified with the reason.
- **Finish with CLOSE.** A feature is not done while its ADR still says "Approved", its `progress.md`
  has stale boxes, or the knowledge base doesn't describe the new behaviour. The knowledge-base
  document is the part future work will read; the plan and specs are not read again.
