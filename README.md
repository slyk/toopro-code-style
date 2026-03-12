# TooPro Code Style Guides

This repository contains comprehensive code style guides used across the TooPro project ecosystem. These guides define the coding standards, conventions, and best practices for multiple programming languages and frameworks.

## Overview

The TooPro code style emphasizes **pragmatism**, **compactness**, and **horizontal space usage** over rigid adherence to conventional style guides. Our philosophy prioritizes working, maintainable code that solves real business problems.

## Core Principles

1. **Practical Over Theoretical** - Favor working solutions over architectural purity
2. **Minimal Spacing Philosophy** - Reduce unnecessary whitespace; compact horizontal code
3. **Direct Communication** - Clear, descriptive naming; comments explain "why" not "what"
4. **Mixed Language Support** - Allow native language (Ukrainian/Russian) comments when natural

## Style Guides

> **Note**: Style guide files live in `plugins/code-style/skills/code-style/references/` —
> this repo is also a Claude Code skill (see [Installing as a Claude Code Skill](#installing-as-a-claude-code-skill) below).

### [General Code Style](plugins/code-style/skills/code-style/references/general.md)
Cross-language patterns and principles applicable to all TooPro projects:
- Naming conventions
- Code organization
- Error handling patterns
- Documentation standards
- Formatting flexibility

### [TypeScript Code Style](plugins/code-style/skills/code-style/references/typescript.md)
TypeScript/NestJS specific style for the **TpsSrv2** microservices monorepo:
- Compact spacing (no space after `if`, `for`, `while`)
- Single-line conditionals without braces
- No space after colons in type annotations: `value:string`
- Heavy use of optional chaining: `item?.value??defaultValue`
- Single quotes for strings

**Technology Stack**: NestJS, TypeScript, Nx monorepo, RabbitMQ, Directus CMS

### [PHP Code Style](plugins/code-style/skills/code-style/references/php.md)
PHP/Drupal 7 specific style for the **TooPro Drupal** backend:
- Compact control structures: `if($condition) { }`
- No space after `=>` in arrays: `'key'=> 'value'`
- Old-style `array()` syntax (PHP 5.3/Drupal 7 compatible)
- Single-line conditionals without braces
- Drupal-specific patterns (hooks, Form API, database queries)

**Technology Stack**: Drupal 7, PHP 5.3+, MySQL, AMF

### [ActionScript 3 Code Style](plugins/code-style/skills/code-style/references/as3.md)
ActionScript3/Adobe Flex specific style for the **tps_retail** point-of-sale interface:
- No space after control keywords: `if(condition) { }`
- No space after colons: `var x:String`
- PureMVC architecture patterns (Proxy, Mediator, Command)
- Underscore prefix for private methods: `_loadedProducts()`
- Strong typing throughout

**Technology Stack**: Adobe Flex 4.x, ActionScript 3.0, PureMVC, AMF

## Key Style Characteristics

### Spacing
- **No space** after `if`, `for`, `while`, `switch` keywords
- **No space** after colons in type annotations
- **Minimal spacing** around operators when it improves compactness

### Braces
- **K&R style** - opening brace on same line
- **Omit braces** for single-line conditionals
- **Inline statements** when appropriate

### Type Annotations
```typescript
// TypeScript
function process(value:string, count:number):boolean { }

// ActionScript3
public function process(value:String, count:Number):Boolean { }
```

### Control Flow
```typescript
// Single-line conditionals
if(!id) return null;
if(value <= 0) value = 0; else value = Math.round(value * 100) / 100;

// Guards
if(!data) throw new Error('missing data');
if(status !== 'active') return;
```

### Arrays and Objects
```typescript
// Compact object literals
const point = {x:10, y:20};
const config = {host:'localhost', port:3000, timeout:5000};

// Arrays
const items = ['item1', 'item2', 'item3'];
```

## Usage

These style guides are designed to be used by:

1. **Developers** - Follow these conventions when writing code for TooPro projects
2. **Code Reviewers** - Reference when reviewing pull requests
3. **AI Code Assistants** - Claude Code, GitHub Copilot, and other AI tools should adhere to these styles
4. **Linters/Formatters** - Configure ESLint, Prettier, and other tools to match these rules

## Project Context

### Related Repositories
- **TpsSrv2** - NestJS microservices monorepo (TypeScript)
- **TooPro Drupal** - Legacy Drupal 7 backend (PHP)
- **tps_retail** - Point-of-sale Flash/Flex interface (ActionScript3)

## Contributing

When contributing to TooPro projects:

1. Read the appropriate language-specific style guide
2. Review the general code style guide for cross-language patterns
3. Follow existing patterns in the codebase
4. Prioritize clarity and correctness over style perfection
5. When in doubt, check similar code in the same project

## Philosophy

> **The best code is code that works, is understood by the team, and can be maintained over time.**

These guides emphasize:
- **Compactness** without sacrificing readability
- **Pragmatism** over dogmatic adherence to standards
- **Consistency** within each project
- **Flexibility** when circumstances require it

## Installing as a Claude Code Skill

This repo is a self-contained Claude Code plugin. On any computer where you've cloned it:

```bash
# Add this repo as a marketplace (once per project)
claude plugin marketplace add /path/to/code_style --scope project

# Install the skill
claude plugin install code-style@toopro-code-style --scope project
```

The skill automatically guides Claude to follow these code style rules whenever it writes or reviews code.

To use across multiple projects, run both commands in each project directory.

## License

These style guides are proprietary to the TooPro project.

---

**Last Updated**: December 2024
**Maintained By**: TooPro Development Team