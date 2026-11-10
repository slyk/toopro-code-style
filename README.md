# TooPro Code Style and Workflow

Two Claude Code plugins used across the TooPro projects. The rules themselves are not repeated here; each lives in exactly one file, linked below.

| Plugin | What it does | Entry file |
|---|---|---|
| `code-style` | The compact, horizontal-first TooPro code style for TypeScript/NestJS, PHP/Drupal 7 and ActionScript3/Flex | [SKILL.md](plugins/code-style/skills/code-style/SKILL.md) |
| `feature-workflow` | A four-phase workflow for significant features: DECIDE (ADR), PLAN, BUILD, CLOSE (knowledge base) | [SKILL.md](plugins/feature-workflow/skills/feature-workflow/SKILL.md) |

## code-style

- [SKILL.md](plugins/code-style/skills/code-style/SKILL.md) — the core rules for all three languages, in three tiers: always, either form, never touch
- [typescript.md](plugins/code-style/skills/code-style/references/typescript.md) — TypeScript/NestJS specifics (**TpsSrv2** monorepo)
- [php.md](plugins/code-style/skills/code-style/references/php.md) — PHP/Drupal 7 specifics (**TooPro Drupal** backend)
- [as3.md](plugins/code-style/skills/code-style/references/as3.md) — ActionScript3/Flex/PureMVC specifics (**tps_retail** point of sale)
- [general.md](plugins/code-style/skills/code-style/references/general.md) — comments, documentation, naming, class layout

The style is for developers, reviewers and AI assistants alike. It differs from Prettier defaults, so do not run a formatter over the code.

## feature-workflow

- [SKILL.md](plugins/feature-workflow/skills/feature-workflow/SKILL.md) — when the workflow applies and the rules
- [workflow.md](plugins/feature-workflow/skills/feature-workflow/references/workflow.md) — the four phases in detail
- [templates/](plugins/feature-workflow/skills/feature-workflow/references/templates/) — ADR, implementation plan, progress, optional split specs

It writes into an `_ai/` folder at the project root: `decisions/`, `implementations/`, `knowledge-base/`.

## Installing

This repo is a Claude Code plugin marketplace. In a project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "toopro-code-style": {
      "source": { "source": "github", "repo": "slyk/toopro-code-style" }
    }
  },
  "enabledPlugins": {
    "code-style@toopro-code-style": true,
    "feature-workflow@toopro-code-style": true
  }
}
```

Or from the command line, once per project:

```bash
claude plugin marketplace add slyk/toopro-code-style --scope project
```

```bash
claude plugin install code-style@toopro-code-style --scope project
```

```bash
claude plugin install feature-workflow@toopro-code-style --scope project
```

To work from a local clone instead, pass its path to `marketplace add`.

## Changing the rules

- Write each rule in one file only. Cross-language formatting goes in `code-style/SKILL.md`; a language file holds only what is specific to that language.
- Make examples marked as correct follow every other rule too — examples are copied more than prose.
- Bump `version` in `.claude-plugin/marketplace.json` and in the plugin's `plugin.json` with every change, so installed copies update.

## License

These guides are proprietary to the TooPro project.
