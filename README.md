# UX Simplifier

An [Agent Skill](https://agentskills.io) that audits UI complexity and directly refactors your code to reduce cognitive load.

Works with **any framework** — Filament, Laravel/Blade, Livewire, React, Vue, Svelte, plain HTML/Tailwind, or mixed stacks.

Applies world-class UX principles (Hick's Law, Miller's Law, progressive disclosure) used by Stripe, Linear, Notion, and Vercel.

## What it does

1. **Scans** your project files and counts UI components (fields, columns, actions, nav items, widgets)
2. **Audits** against established UX principles with a scored report
3. **Refactors** the code directly (with your approval) — removes clutter, groups actions, sections forms, hides non-essential columns

## Installation

### Claude Code (plugin — recommended)

```
/plugin marketplace add erenilhan/ux-simplifer
/plugin install ux-simplifier
```

### Claude Code (global — all projects)

```bash
# Clone into your global skills directory
git clone https://github.com/erenilhan/ux-simplifer.git ~/.claude/skills/ux-simplifier
```

### Claude Code (per-project — shared with team)

```bash
# Add to your project repo
mkdir -p .claude/skills
git clone https://github.com/erenilhan/ux-simplifer.git .claude/skills/ux-simplifier
```

### Cursor / GitHub Copilot / Other Agent Skills compatible tools

Copy the `ux-simplifier/` folder into your project's `.skills/` or `.claude/skills/` directory (check your tool's docs for the exact path).

## Usage

Just tell your AI agent:

- *"This page is too complex, simplify it"*
- *"UX audit on this resource"*
- *"Too many buttons, help me clean up"*
- *"Bu formu sadeleştir"* (works in Turkish too)

The skill triggers automatically based on context.

## Structure

```
├── .claude-plugin/
│   └── marketplace.json          # Marketplace manifest for plugin install
├── ux-simplifier/
│   ├── .claude-plugin/
│   │   └── plugin.json           # Plugin metadata
│   └── skills/
│       └── ux-simplifier/
│           ├── SKILL.md          # Main instructions (scan → audit → refactor)
│           └── references/
│               ├── principles.md # UX laws + how top products apply them
│               └── patterns.md   # 10 refactor patterns with code examples
```

## Supported Frameworks

| Framework | Support |
|---|---|
| Filament (PHP) | Full — actions, forms, tables, navigation, widgets |
| Laravel Blade / Livewire | Full |
| React / Next.js | Full |
| Vue / Nuxt | Full |
| Svelte | Full |
| Plain HTML / Tailwind | Full |
| Any other web framework | Principles apply, patterns adapt |

## UX Principles Applied

- **Hick's Law** — fewer choices = faster decisions
- **Miller's Law** — 7±2 items in working memory
- **Fitts's Law** — important targets should be large and close
- **Jakob's Law** — users expect your UI to work like others they know
- **Gestalt Proximity** — visual grouping = information architecture
- **Progressive Disclosure** — show only what's needed now

## License

MIT
