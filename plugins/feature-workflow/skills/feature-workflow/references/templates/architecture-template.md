# System Architecture

**Last updated:** {YYYY-MM-DD}

## Overview

High-level description of the system, its purpose, and key characteristics.

## System Diagram

```
[Component A] --HTTP--> [Component B]
     |                       |
     |--MQ--> [Component C] --DB--> [Database]
```

> Use text-based diagrams. Keep them simple and focused on communication patterns.

## Services / Modules

### {Service/Module Name}
- **Purpose:** {responsibility}
- **Location:** `{path}`
- **Communicates with:** {list of other services/modules}
- **Key patterns:** {notable architectural patterns used}

## Communication Patterns

Describe how parts of the system talk to each other:
- **Synchronous:** HTTP/REST, gRPC, direct function calls
- **Asynchronous:** Message queues, event bus, webhooks
- **Data sharing:** Shared database, API contracts, shared types

## Data Model

Key entities and their relationships:
- **{Entity A}** — {description}, relates to {Entity B} via {relation}
- **{Entity B}** — {description}

## External Integrations

| System | Purpose | Protocol |
|--------|---------|----------|
| {name} | {what it does} | REST / MQ / SDK |

## Key Design Decisions

- {Decision 1} — {rationale}
- {Decision 2} — {rationale}

## Conventions

Project-specific conventions that aren't obvious from the code:
- {Convention 1}
- {Convention 2}
