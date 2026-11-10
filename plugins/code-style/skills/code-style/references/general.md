# General Code Style Guide

Cross-language habits of the TooPro codebases (PHP, ActionScript3, TypeScript) that are **not** about spacing or braces. Formatting rules live in `SKILL.md`; language specifics live in `typescript.md`, `php.md`, `as3.md`. Each rule is written in one place only.

Read this file when you write comments or docs, name something new, or lay out a new class or file.

## Principles

- **Practical over theoretical**: a working, direct solution beats architectural purity. Mixed approaches are fine when they serve the business need.
- **Compact and horizontal**: fewer lines, less whitespace, as long as it still reads well.
- **Comments explain why, not what.**

## Comments and Documentation

### Function Documentation

Give a public function or method a doc block (`/** */`, JSDoc/PHPDoc style) when its behaviour is not obvious from its name and types: side effects, units, special values, what a `null` return means, when to call it, why it exists. Then include:
- A brief description of what it does and why
- Parameters whose meaning the type doesn't convey
- What is returned in each case
- A usage example for complex functions

Do not add a doc block that only restates the signature (`@param id the id`), and do not strip existing doc blocks. Language files show the exact doc-block form.

### Inline Comments

Use single-line comments for field/constant descriptions, complex logic and warnings:

```php
$timeout = 5000; // milliseconds
// fix phones with old format
if(strlen($phone)==13 && substr($phone, 0, 4) == '0380') $phone = substr($phone, 1);
```

### Mixed Language Comments

Comments in Ukrainian/Russian are allowed alongside English:
- English for public API documentation
- Native language for internal implementation notes, mixed naturally

```typescript
// обробка транзакції з кешбеком
const cashback = await calculateCashback(transaction);

/**
 * Sends notification to queue
 * відправляє нотифікацію в чергу обробки
 */
```

Never translate or remove existing native-language comments.

### File-Top Design Notes

Large doc blocks at the top of a file that describe the overall algorithm or design decisions (often in Ukrainian/Russian) are documentation, not dead comments. Never strip them. Examples: `tps_pay_method.module` opens with a numbered fiscalization algorithm; `ConfirmBoxMediator.as` explains how mediators work.

### Commented-Out Code — Keep With Reason

Commented-out blocks are kept when they document a decision. Always attach the reason:

```php
/*'cron'=> array( //dont used, because receip_check do the cron job when needed
    ...
),*/
```

Commented debug lines may stay too if they might be useful again:

```php
//echo "tps core subm"; print_r($node); die();
```

### Closing-Bracket Trailing Markers

On deeply nested structures, a trailing comment marks what the bracket closes:

```php
    ))//rro
);
```

### Debug Statements

Debug output (`console.log`, `trace()`, `watchdog(... WATCHDOG_DEBUG)`) is allowed during development. Include context in the message and keep sensitive data out of it.

## Naming

Standard casing: **PascalCase** classes, **camelCase** methods and local variables, **UPPER_SNAKE_CASE** constants. Project-specific parts:

- Project classes are often prefixed: `TPSCore`, `TPSTransAPI`.
- Value Objects carry a `VO` suffix: `ProductVO`, `UpdatesVO`, `TransactionVO`.
- PHP variables that mirror database fields use **snake_case** (`$phone_number`); Drupal functions follow `modulename_descriptive_name`.
- Method names start with a verb (`loadProducts`, `filterProducts`). Avoid abbreviations unless they are already common in the codebase (`srvc`, `lgr`, `spp`, `vw`).
- Related constants are grouped in one class with a shared prefix (`TS_FINISHED`, `TS_DELETED`).

## Code Organization

- One primary class per file; small helper classes may share it.
- Functions are typically 20-100 lines. A long function is fine when it handles one logical flow; split only when it improves clarity.
- Class member order: constants, static properties, instance properties, constructor, public methods, protected/private methods, static methods.

## Error Messages

Put the relevant IDs and values into the message, and log with a fitting severity:

```php
if(!$node) return services_error('No node with such nid.');
watchdog('TPS', "Trans !tid added", array('!tid' => $tLink), WATCHDOG_INFO);
```

## Indentation and Blank Lines

- Indentation: follow the file. PHP typically 4 spaces or 1 tab, ActionScript3 1 tab, TypeScript 2 spaces.
- One blank line between functions; use blank lines sparingly inside a function to group related statements.

## When in Doubt

1. Follow existing patterns in the file you are editing
2. Prioritize clarity and correctness
