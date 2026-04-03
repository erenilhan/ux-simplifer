# UX Simplification Principles

This reference contains the core UX principles that drive audit decisions. Read this when you need to justify WHY something is overwhelming — not just that it exceeds a threshold.

---

## Core Laws

### Hick's Law
Decision time increases logarithmically with the number of choices. 3 actions = fast pick. 12 actions = paralysis.

**Practical meaning:** Every button, link, tab, and dropdown option on screen is a choice. The user's brain must evaluate each one before acting. Reducing choices isn't removing features — it's removing friction.

**Apply when:** Too many actions, buttons, nav items, or options visible at once.

**Fix patterns:**
- Group related actions behind a single trigger (dropdown, action group, "More" menu)
- Use progressive disclosure — show primary actions, hide secondary behind a click
- Default the most common choice; let users override only when needed

### Miller's Law
Working memory holds 7±2 items. Beyond that, users start forgetting what they've already seen.

**Practical meaning:** A form with 15 visible fields means the user can't hold the whole form in their head. They scroll back and forth, lose context, make mistakes.

**Apply when:** Too many visible form fields, table columns, dashboard widgets, or nav items.

**Fix patterns:**
- Break forms into sections, tabs, or wizard steps (each ≤7 fields)
- Hide non-essential table columns by default (toggleable)
- Chunk navigation into logical groups (≤5 items per group)

### Fitts's Law
Time to reach a target depends on distance and size. Small, far-away buttons are hard to click.

**Practical meaning:** Critical actions should be large and close to where the user's attention already is. Destructive actions should be harder to reach (not just "are you sure?" dialogs).

**Apply when:** Important actions are buried, tiny, or far from the content they affect.

**Fix patterns:**
- Primary actions = prominent, near the content
- Destructive actions = smaller, separated, requiring confirmation
- Touch targets minimum 44×44px on mobile

### Jakob's Law
Users spend most of their time on OTHER sites. They expect your interface to work like the ones they already know.

**Practical meaning:** Don't reinvent navigation patterns. If every SaaS puts the main nav on the left sidebar, putting yours on the right bottom is "creative" but unusable. Custom UI patterns have a learning cost.

**Apply when:** Custom widgets, unusual navigation, non-standard form patterns.

**Fix patterns:**
- Use platform conventions (sidebar nav, top header, standard form layouts)
- If you must innovate, do it in ONE place — not everywhere
- Test: would a new user find this in 5 seconds?

### Law of Proximity (Gestalt)
Items close together are perceived as related. Items far apart are perceived as unrelated.

**Practical meaning:** Visual grouping IS information architecture. If "Save" and "Delete" are next to each other with no spacing, users will accidentally delete. If related fields are scattered across a form, users won't see the connection.

**Apply when:** Layout doesn't reflect logical grouping. Related items are far apart. Unrelated items are too close.

**Fix patterns:**
- Group related fields/actions with whitespace or containers
- Separate dangerous actions from safe ones
- Use consistent spacing: tight within groups, loose between groups

---

## Progressive Disclosure

The single most powerful simplification technique. Show only what's needed NOW; reveal the rest on demand.

### Levels of disclosure

1. **Always visible:** The 20% of features used 80% of the time
2. **One click away:** Features used regularly but not every time (expandable sections, tabs)
3. **Two clicks away:** Features used rarely (settings, advanced options, bulk operations)
4. **Hidden until needed:** Features triggered by context (conditional fields, contextual actions)

### How top products apply this

**Stripe Dashboard:** The main view shows recent payments, a chart, and 3 key metrics. Everything else (disputes, payouts, billing, radar) lives in the sidebar — one click away. Advanced settings are 2+ clicks deep. You never see webhook configuration on the dashboard.

**Linear:** Issues list shows: title, status, assignee, priority. That's it — 4 columns. Labels, estimates, cycles, projects — all accessible but not in your face. The create form has 2 required fields (title, team). Everything else is optional and hidden behind "+" buttons.

**Notion:** A new page is blank. You add what you need. The toolbar appears on hover. Properties are collapsed by default. This is extreme progressive disclosure — start with nothing, build up.

**Vercel:** Deploy dashboard shows: project name, status, last deploy time, branch. Clicking into a project reveals build logs, environment variables, domains. Settings → Advanced → even more. Three clear depth levels.

### The pattern
All these products follow the same structure:
- **Surface:** 3-5 key data points or actions
- **First level:** 8-12 secondary features
- **Deep:** Everything else

If your UI shows 15+ things at the surface level, you're skipping progressive disclosure entirely.

---

## Cognitive Load Types

### Intrinsic load
The inherent complexity of the task. Filing a tax return IS complex — you can't simplify the tax code.

**What to do:** Accept it. Focus on reducing the other two types.

### Extraneous load
Complexity added by BAD DESIGN. Confusing labels, inconsistent patterns, unnecessary steps, visual clutter.

**What to do:** Eliminate ruthlessly. This is where most wins come from.

**Common sources:**
- Inconsistent button styles (is this clickable?)
- Jargon labels that don't match user mental models
- Required fields that aren't actually required
- Decorative elements that compete with content
- Redundant information shown in multiple places

### Germane load
Effort spent LEARNING the interface patterns. Acceptable if the patterns are consistent and transferable.

**What to do:** Invest in consistency. If a user learns one form, every form should work the same way.

---

## Visual Hierarchy Checklist

When auditing a screen, check these in order:

1. **What does the user see first?** (Should be the primary action or most important data)
2. **What does the user see second?** (Should be supporting context)
3. **Is there a clear path?** (Can the user complete their task without wandering?)
4. **What can be removed?** (If removing it doesn't hurt task completion, it shouldn't be there)
5. **What can be hidden?** (If it's needed <20% of the time, it can be progressive)
6. **What can be combined?** (Two related actions → one action group)

---

## Red Flags — Universal (Framework-agnostic)

These are warning signs regardless of technology:

| Signal | Why it's a problem | Typical fix |
|---|---|---|
| 10+ visible form fields | Exceeds working memory | Section/tab/wizard |
| 8+ table columns (default visible) | User can't scan a row | Hide non-essential, make toggleable |
| 6+ action buttons in one area | Choice paralysis | Group into dropdown/menu |
| 3+ levels of nested UI | Lost context, can't navigate back | Flatten, use breadcrumbs |
| No visual grouping | Everything looks equally important | Add sections, whitespace, headers |
| Every field is "required" | Nothing is prioritized | Audit true requirements, mark optional |
| Confirmation dialogs everywhere | Cry wolf — users click through them | Use undo instead of confirm |
| Sidebar with 15+ items | Navigation becomes a search problem | Group, collapse, prioritize |
| Dashboard with 8+ widgets | Nothing stands out | Reduce to 3-4 key metrics |
| Mixed action styles | User can't predict what's clickable | Consistent button/link hierarchy |
