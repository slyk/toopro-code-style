# Implementation Plan: {Feature Title}

**ADR:** ADR-{N}-{slug}
**Date:** {YYYY-MM-DD}
**Status:** Planning | In Progress | Complete

## Scope

Brief description of what will be built.

## Acceptance Criteria

- [ ] AC1: {description}
- [ ] AC2: {description}
- [ ] AC3: {description}

## Affected Projects

> For Nx monorepos: list all affected apps and libs. For non-monorepo projects, list affected modules/directories.

| Project / Module | Type | Role in feature |
|------------------|------|-----------------|
| `{project-name}` | App / Lib | {description} |

## Files to Create

| File | Project | Description |
|------|---------|-------------|
| `path/to/file` | `{project}` | Description |

## Files to Modify

| File | Project | Changes |
|------|---------|---------|
| `path/to/file` | `{project}` | Description of changes |

## Database Changes (Proposals)

> These are proposals for the DB engineer. Describe what the data layer needs, not how to migrate.

| Collection / Table | Change | Description |
|--------------------|--------|-------------|
| `{name}` | New field / New collection / Modify | {details} |

## Dependencies

- **External:** {libraries, APIs, services}
- **Internal:** {other modules, features that must exist first}

## Build Order

1. {First layer — typically shared types/libs}
2. {Second layer — typically backend/data}
3. {Third layer — typically frontend/UI}
4. Integration testing

## New Lib Decision

> If this feature might need a new Nx library, document the decision here. If unsure, ask the user.

- **New lib needed?** Yes / No / Ask user
- **Proposed name:** `{lib-name}`
- **Location:** `libs/{category}/{lib-name}`
- **Rationale:** {why a new lib vs. extending existing}

> Remove this section if not applicable or if the project is not an Nx monorepo.

## Risk & Mitigation

| Risk | Impact | Mitigation |
|------|--------|------------|
| {risk} | High / Med / Low | {mitigation} |
