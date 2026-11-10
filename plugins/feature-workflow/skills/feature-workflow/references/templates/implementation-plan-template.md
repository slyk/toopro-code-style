# Implementation Plan: {Feature Title}

**ADR:** ADR-{N}-{slug}
**Date:** {YYYY-MM-DD}
**Status:** Planning | In Progress | Complete

> This file is the whole plan by default. Split an area into `backend-implementation.md`, `frontend-implementation.md` or `shared-implementation.md` only when separate agents build the parts or the plan passes roughly 300 lines; then keep a one-line summary and a link here, and never write the same change twice.
>
> Refer to code by file path plus class/method name. No line numbers.

## Scope

Brief description of what will be built. For the reasoning, see the ADR.

## Acceptance Criteria

- [ ] AC1: {description}
- [ ] AC2: {description}
- [ ] AC3: {description}

## Affected Projects

> For Nx monorepos: list all affected apps and libs. For non-monorepo projects, list affected modules/directories. Include other codebases that must change (and who changes them).

| Project / Module | Type | Role in feature |
|------------------|------|-----------------|
| `{project-name}` | App / Lib / Other codebase | {description} |

## Changes

> One subsection per area, in build order. Remove areas that don't apply.

### Shared (types, DTOs, contracts)

| File | Symbol | Change |
|------|--------|--------|
| `path/to/file` | `{TypeOrFunction}` | New / Modified — {what and why} |

### Backend

| File | Symbol | Change |
|------|--------|--------|
| `path/to/file` | `{Service.method()}` | New / Modified — {what and why} |

### Frontend / Client

| File | Symbol | Change |
|------|--------|--------|
| `path/to/file` | `{Component}` | New / Modified — {what and why} |

## Contracts

> Messages, queues, API endpoints or shared types whose shape changes. Remove if none.

| Contract | Change | Consumers to update |
|----------|--------|---------------------|
| `{queue / endpoint / type}` | {payload or signature change} | {who reads it} |

## Database Changes (Proposals)

> The only place data changes are listed. These are proposals for the DB engineer: describe what the data layer needs, not how to migrate.

| Collection / Table | Change | Type / Constraint / Index | Why |
|--------------------|--------|---------------------------|-----|
| `{name}.{field}` | New field / New collection / Modify | {type, unique, index} | {reason} |

## Dependencies

- **External:** {libraries, APIs, services}
- **Internal:** {other modules, features that must exist first}

## Build Order

1. {First layer — typically shared types/libs}
2. {Second layer — typically backend/data}
3. {Third layer — typically frontend/UI}

## Verification

> How each acceptance criterion will be checked. Use what the project really has: automated tests where a test setup exists, otherwise a build, lint, a manual scenario, or a check done by the user. Say who runs it.

| Criterion | How it is checked | Command / steps | Who |
|-----------|-------------------|-----------------|-----|
| AC1 | Unit test / Build / Manual scenario / User check | `{command}` or steps | Agent / User |

## New Lib Decision

> If this feature might need a new Nx library, document the decision here. If unsure, ask the user. Remove this section if not applicable.

- **New lib needed?** Yes / No / Ask user
- **Proposed name:** `{lib-name}`
- **Location:** `libs/{category}/{lib-name}`
- **Rationale:** {why a new lib vs. extending existing}

## Risk & Mitigation

| Risk | Impact | Mitigation |
|------|--------|------------|
| {risk} | High / Med / Low | {mitigation} |
