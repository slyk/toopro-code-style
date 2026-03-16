---
name: feature-workflow
description: >
  AI-driven feature development workflow. USE THIS SKILL when the user requests a new feature,
  significant change, or says "new feature", "add feature", "implement X", "build X", or similar.
  Guides AI agents through 3 phases: DECIDE (ADR), PLAN (implementation specs), BUILD (execute & track).
  Ensures architectural consistency, traceability, and quality across any tech stack.
  Also trigger when user mentions "ADR", "architecture decision", "implementation plan", or "feature workflow".
---

# Feature Development Workflow

This skill defines the **3-phase methodology** for developing new features using AI agents.
Every new feature or significant change MUST follow this workflow.

**Read the full workflow guide:** `references/workflow.md`

## Quick Reference

| Phase | Output | Gate |
|-------|--------|------|
| **DECIDE** | `_ai/decisions/ADR-{N}-{slug}.md` | User approval required |
| **PLAN** | `_ai/implementations/{slug}/` folder | User approval required |
| **BUILD** | Code + `progress.md` updates | Tests pass |

## When Triggered

1. **Read `references/workflow.md`** for the complete process
2. **Check `_ai/decisions/`** for existing ADRs (to determine next ADR number)
3. **Check `_ai/knowledge-base/`** for relevant patterns and guides about existing subsystems
4. **Check project's `CLAUDE.md` or `AGENTS.md`** for architecture info, lib structure, and conventions
5. **Follow the 3 phases** with user approval gates between each

## Templates

All templates are in `references/templates/`:

| Template | When to use |
|----------|-------------|
| `adr-template.md` | Phase 1: Create architecture decision record |
| `implementation-plan-template.md` | Phase 2: Main plan with scope and acceptance criteria |
| `backend-implementation-template.md` | Phase 2: Backend/API changes (optional, use when feature has backend work) |
| `frontend-implementation-template.md` | Phase 2: Frontend/UI changes (optional, use when feature has frontend work) |
| `shared-implementation-template.md` | Phase 2: Shared libs/types (optional, use when feature affects shared code) |
| `progress-template.md` | Phase 2: Task checklist for tracking build progress |
| `architecture-template.md` | Post-build: Update or create architecture docs |

## Critical Rules

- **Never skip phases.** Even small features need at least a lightweight ADR.
- **Never start coding without an approved plan.** The plan is the contract.
- **Always update `progress.md`** as you complete tasks during BUILD.
- **Always update architecture docs** after significant changes.
- **Always check knowledge-base** before starting — it may contain patterns for similar work.
- **Agents MUST read their implementation spec** before writing code.
