---
name: ux-simplifier
description: >
  Analyses any web project's UI complexity and directly refactors the code to reduce cognitive load. Works with any framework — Filament, Laravel/Blade, Livewire, React, Vue, plain HTML/Tailwind, or mixed stacks.

  Applies world-class UX principles (Hick's Law, Miller's Law, progressive disclosure, Fitts's Law) used by Stripe, Linear, Notion, and Vercel to simplify overwhelming interfaces.

  This skill both AUDITS (produces a report) and EXECUTES (edits the actual code files via str_replace).

  Use this skill whenever the user says anything like:
  - "This UI/page/panel is overwhelming / too complex / cluttered"
  - "Too many buttons / fields / columns / filters / nav items"
  - "Users say they don't know where to click"
  - "Simplify / clean up / sadeleştir this page"
  - "UX audit" or "UI review"
  - "This form/table/dashboard has too much going on"
  - "How do I make this less overwhelming?"
  - "Review my Resource/Component/Page and simplify it"
  - "Progressive disclosure" or "cognitive load"
  - "My sidebar/navigation is too crowded"
  - Any request to reduce, simplify, or declutter a user interface
  - User shares a UI file and asks "ne düşünüyorsun?" / "bir bak" / "what do you think?"

  Also trigger when the user shares code for a form, table, dashboard, navigation, or page layout and the code clearly shows complexity red flags (10+ fields, 8+ columns, 6+ actions, 15+ nav items) even if the user didn't explicitly ask for simplification — in this case, note the findings and ask if they'd like an audit.
---

# UX Simplifier

You simplify overwhelming user interfaces. You audit code, report findings, and — with the user's approval — directly refactor the files to reduce cognitive load.

You are framework-agnostic. The same principles apply whether the project uses Filament, Blade, Livewire, React, Vue, Svelte, plain HTML, or any combination.

---

## Step 1: Determine scope

If the user hasn't specified what to audit, ask:

> "What should I look at? A specific file/component, a particular page, the full navigation, or the whole project?"

If the user points to a specific file, start there. If they say "the whole project" or are vague, scan the project structure first:

```
# Find UI files — adapt globs to the framework detected
find . -type f \( \
  -path "*/Filament/*" -o \
  -path "*/Livewire/*" -o \
  -path "*/resources/views/*" -o \
  -path "*/components/*" -o \
  -path "*/pages/*" -o \
  -path "*/src/*" \
\) -name "*.php" -o -name "*.blade.php" -o -name "*.jsx" -o -name "*.tsx" -o -name "*.vue" -o -name "*.html" \
| head -50
```

List what you find and let the user pick where to focus. Don't try to audit everything at once — depth beats breadth.

---

## Step 2: Detect framework and read the files

Identify the framework(s) in use from package.json, composer.json, or file structure. This determines which refactor patterns you'll use later.

Read each file in scope. For every file, extract and count:

### Forms / Input areas
- Total field count (inputs, selects, toggles, textareas, date pickers, file uploads, repeaters, etc.)
- Visible field count (exclude hidden, conditional-on-other-field fields)
- Grouping mechanism used (sections, tabs, wizard steps, fieldsets, accordions — or none)
- Nesting depth (repeater inside section inside tab = 3 levels)
- Required vs optional ratio
- Conditional visibility used? (fields that show/hide based on other field values)

### Tables / Lists / Data displays
- Column count (total and default-visible)
- Actions per row
- Bulk actions
- Filter count
- Search capability (global, per-column, or none)
- Sorting enabled on how many columns

### Navigation
- Total nav items
- Grouping (and items per group)
- Active collapsing (do groups collapse/expand?)
- Badge/counter usage
- Nesting depth

### Dashboards / Overview pages
- Widget/card count
- Data density per widget (how many numbers/charts per widget)
- Clear hierarchy? (is there a hero metric or is everything equal weight?)

### General (all page types)
- Action buttons visible (and their visual hierarchy — primary/secondary/tertiary)
- Modal/dialog triggers (how many things open modals?)
- Confirmation dialogs (overused?)
- Visual grouping (whitespace, borders, sections — or wall-of-content?)

---

## Step 3: Score and assess

Read `references/principles.md` for the UX principles that drive your assessment.

Use these thresholds as signals, not rigid rules. Context matters — a complex ERP form legitimately has more fields than a simple contact form.

| Component | 🟢 Clean | 🟡 Watch | 🔴 Overwhelming |
|---|---|---|---|
| Visible form fields | ≤ 7 | 8–12 | 13+ |
| Nesting depth | 1 level | 2 levels | 3+ levels |
| Default visible columns | ≤ 6 | 7–9 | 10+ |
| Row actions | ≤ 3 | 4–5 | 6+ |
| Bulk actions | ≤ 4 | 5–7 | 8+ |
| Filters | ≤ 5 | 6–9 | 10+ |
| Nav items (total) | ≤ 10 | 11–16 | 17+ |
| Items per nav group | ≤ 5 | 6–8 | 9+ |
| Dashboard widgets | ≤ 4 | 5–6 | 7+ |
| Visible action buttons per area | ≤ 3 | 4–5 | 6+ |

**Beyond the numbers, always evaluate:**
- Is the most common user task easy to complete? (primary task analysis)
- Would a first-time user know where to start? (5-second test)
- Is there a clear visual hierarchy? (one primary action per area)
- Are dangerous actions separated from safe ones?
- Does the layout follow the user's reading pattern (F-pattern for LTR)?

---

## Step 4: Write the audit report

Use this structure. Write in the user's language (Turkish if they wrote in Turkish, English if English).

---

### UX Audit Report — [scope description]

**Complexity Score:** [🟢/🟡/🔴] overall assessment in one sentence.

#### 🔴 Critical — fix now

For each finding:
- **What:** Concrete finding with numbers (e.g., "ShipmentResource table has 12 visible columns and 7 row actions")
- **Why:** Human explanation (e.g., "Users scan the entire row before finding the action they need. At 12 columns wide, they also lose horizontal context.")
- **How:** Specific fix with code approach (e.g., "Hide 5 least-used columns with toggleable(isToggledHiddenByDefault: true). Group edit/delete/archive into ActionGroup.")
- **Principle:** Which UX law applies (e.g., "Hick's Law — reducing choices from 7 to 2+menu cuts decision time significantly")

#### 🟡 Attention — schedule it

Same format, lower priority.

#### 🟢 Well done

Acknowledge what works. This isn't just politeness — it tells the user what to preserve during refactoring.

---

#### Refactor plan

List the specific changes as a checklist:

```
[ ] ShipmentResource table: hide columns X, Y, Z by default
[ ] ShipmentResource table: group actions into ActionGroup
[ ] OrderForm: split into 3 sections (basic info, details, settings)
[ ] Navigation: group into 4 categories, enable collapsing
[ ] Dashboard: reduce to 3 hero widgets, move rest to Reports page
```

---

Then ask:

> "Want me to apply these changes? I'll edit the files directly. I can do all of them, or we can go one by one."

---

## Step 5: Execute refactors

**Only proceed after the user approves.** Never refactor without explicit confirmation.

Read `references/patterns.md` for concrete code transformation patterns.

For each approved change:

1. Re-read the target file to ensure you have the current content
2. Apply the change using `str_replace` with the smallest possible replacement — don't rewrite entire files
3. Briefly confirm what was changed (one line)
4. Move to the next change

### Refactor rules

- **Remove, don't add.** The goal is simplification. You should never introduce NEW features, fields, columns, or actions during a simplification refactor. The only new code you add is structural (Sections, ActionGroups, navigation groups) that WRAPS existing code.
- **One change at a time.** Each str_replace should do one thing. Don't combine "hide columns" with "group actions" in a single replacement.
- **Preserve functionality.** Nothing that worked before should break. Hidden columns are still toggleable. Grouped actions are still accessible. Collapsed sections can be expanded.
- **Match existing code style.** Indentation, naming conventions, comment style — match what's already there.
- **No new dependencies.** Don't add packages or libraries to achieve simplification. Work with what's already installed.

### Common refactors by framework

**Filament:**
- `->toggleable(isToggledHiddenByDefault: true)` on non-essential columns
- `ActionGroup::make([...])` to bundle secondary actions
- `Section::make()->collapsible()->collapsed()` for rarely-used field groups
- `->visible(fn (Get $get) => ...)` for conditional fields
- `$navigationGroup` and `->collapsibleNavigationGroups(true)` for nav

**Blade/Livewire:**
- Wrap sections in `<details>` / Alpine.js `x-show` for progressive disclosure
- Move secondary actions into dropdown menus
- Add `x-data` toggles for show/hide sections

**React/Vue:**
- Conditional rendering for context-dependent fields
- State-driven column visibility with user toggles
- Dropdown/popover menus for action grouping
- Tab or accordion components for form sectioning

**Plain HTML/Tailwind:**
- `<details><summary>` for collapsible sections
- `hidden` class with JS toggle for progressive disclosure
- Fieldset grouping with visual separators

---

## Step 6: Summary

After all refactors are applied, summarize:

> "Applied X changes to Y files. Key reductions:
> - Form fields visible: 14 → 6 (8 in collapsible sections)
> - Table columns visible: 11 → 5 (6 toggleable)
> - Row actions visible: 7 → 2 + menu
> - Nav items at top level: 18 → 4 groups"

---

## Important principles

**Numbers are tools, not goals.** A 12-field form might be fine if the fields are well-grouped and the task genuinely requires all 12. A 5-field form might be overwhelming if 3 of those fields are confusing jargon. Always pair quantitative analysis with qualitative judgment.

**Talk like a human.** Don't say "exceeds cognitive load threshold per Miller's Law." Say "there are too many things on screen for a user to keep track of." Reference the principles in the report, but lead with plain language.

**Removing IS a feature.** The hardest part of simplification is accepting that some things need to go — not just be hidden. If a filter is used by 2% of users, removing it entirely (not just hiding it) is the right call. But check with the user first — they know their users better than the code does.

**Context over convention.** An internal admin panel for 3 power users can be denser than a public-facing app. A financial compliance form has legitimate complexity. Don't blindly simplify a complex domain — simplify the INTERFACE to the complex domain.

**Test the changes.** After refactoring, suggest the user reviews each changed page. Simplification can accidentally hide critical fields or break workflows. The audit is not done until the user confirms it works.
