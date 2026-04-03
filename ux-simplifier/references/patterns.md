# Refactor Patterns

Concrete code transformations for reducing UI complexity. Organized by pattern, with framework-specific examples.

Read this file when you're ready to EXECUTE refactors — after the audit report is approved.

---

## Pattern 1: Group Actions

**Problem:** 5+ action buttons in a row. User scans all of them every time.

**Solution:** Keep 1-2 primary actions visible. Group the rest.

### Filament (PHP)
```php
// BEFORE: 6 visible actions
Tables\Actions\ViewAction::make(),
Tables\Actions\EditAction::make(),
Tables\Actions\DeleteAction::make(),
Tables\Actions\ReplicateAction::make(),
Tables\Actions\RestoreAction::make(),
Tables\Actions\ForceDeleteAction::make(),

// AFTER: 2 visible + grouped
Tables\Actions\ViewAction::make(),
Tables\Actions\EditAction::make(),
Tables\Actions\ActionGroup::make([
    Tables\Actions\ReplicateAction::make(),
    Tables\Actions\RestoreAction::make(),
    Tables\Actions\DeleteAction::make()
        ->requiresConfirmation(),
    Tables\Actions\ForceDeleteAction::make()
        ->requiresConfirmation(),
])->icon('heroicon-m-ellipsis-vertical')
  ->tooltip('More actions'),
```

### Blade/Livewire
```html
<!-- BEFORE -->
<div class="flex gap-2">
    <button>View</button>
    <button>Edit</button>
    <button>Delete</button>
    <button>Duplicate</button>
    <button>Archive</button>
    <button>Export</button>
</div>

<!-- AFTER -->
<div class="flex gap-2">
    <button>View</button>
    <button>Edit</button>
    <x-dropdown>
        <x-slot:trigger>
            <button>More</button>
        </x-slot:trigger>
        <x-dropdown.item>Duplicate</x-dropdown.item>
        <x-dropdown.item>Archive</x-dropdown.item>
        <x-dropdown.item>Export</x-dropdown.item>
        <x-dropdown.divider />
        <x-dropdown.item class="text-red-600">Delete</x-dropdown.item>
    </x-dropdown>
</div>
```

### React/JSX
```jsx
// BEFORE: all actions visible
<div className="flex gap-2">
  {actions.map(action => <Button key={action.id}>{action.label}</Button>)}
</div>

// AFTER: primary visible, rest in dropdown
<div className="flex gap-2">
  <Button onClick={onView}>View</Button>
  <Button onClick={onEdit}>Edit</Button>
  <DropdownMenu>
    <DropdownTrigger><Button variant="ghost">⋯</Button></DropdownTrigger>
    <DropdownContent>
      {secondaryActions.map(a => (
        <DropdownItem key={a.id} onClick={a.handler}>{a.label}</DropdownItem>
      ))}
    </DropdownContent>
  </DropdownMenu>
</div>
```

---

## Pattern 2: Section Forms

**Problem:** 10+ form fields visible at once. User scrolls, loses context.

**Solution:** Group related fields into collapsible sections, tabs, or wizard steps.

### Filament
```php
// BEFORE: flat list of 14 fields
Forms\Components\TextInput::make('name'),
Forms\Components\TextInput::make('email'),
Forms\Components\TextInput::make('phone'),
Forms\Components\TextInput::make('company'),
Forms\Components\TextInput::make('address'),
// ... 9 more fields

// AFTER: grouped into sections
Forms\Components\Section::make('Contact Information')
    ->schema([
        Forms\Components\TextInput::make('name')->required(),
        Forms\Components\TextInput::make('email')->required(),
        Forms\Components\TextInput::make('phone'),
    ]),
Forms\Components\Section::make('Company Details')
    ->schema([
        Forms\Components\TextInput::make('company'),
        Forms\Components\TextInput::make('address'),
        Forms\Components\TextInput::make('tax_number'),
    ])
    ->collapsible()
    ->collapsed(), // starts closed — progressive disclosure
Forms\Components\Section::make('Preferences')
    ->schema([
        // rarely-used fields here
    ])
    ->collapsible()
    ->collapsed(),
```

### General HTML/Tailwind
```html
<!-- BEFORE: wall of inputs -->
<form class="space-y-4">
    <input type="text" placeholder="Name" />
    <input type="email" placeholder="Email" />
    <!-- ... 12 more inputs ... -->
</form>

<!-- AFTER: accordion sections -->
<form class="space-y-6">
    <fieldset>
        <legend class="text-lg font-medium">Contact Information</legend>
        <div class="mt-3 space-y-3">
            <input type="text" placeholder="Name" required />
            <input type="email" placeholder="Email" required />
        </div>
    </fieldset>
    <details>
        <summary class="text-lg font-medium cursor-pointer">Company Details</summary>
        <div class="mt-3 space-y-3">
            <input type="text" placeholder="Company" />
            <input type="text" placeholder="Tax Number" />
        </div>
    </details>
</form>
```

---

## Pattern 3: Hide Table Columns

**Problem:** 10+ table columns. Row becomes a horizontal scroll nightmare.

**Solution:** Show essential columns. Make others toggleable, hidden by default.

### Filament
```php
// Add to non-essential columns:
Tables\Columns\TextColumn::make('internal_code')
    ->toggleable(isToggledHiddenByDefault: true),
Tables\Columns\TextColumn::make('created_at')
    ->toggleable(isToggledHiddenByDefault: true),
Tables\Columns\TextColumn::make('updated_at')
    ->toggleable(isToggledHiddenByDefault: true),

// Essential columns stay always visible:
Tables\Columns\TextColumn::make('name')
    ->searchable()
    ->sortable(),
Tables\Columns\TextColumn::make('status')
    ->badge(),
```

### React Table / Generic
```jsx
// Define column visibility defaults
const defaultVisibility = {
  name: true,
  status: true,
  assignee: true,
  // hidden by default — user can toggle
  created_at: false,
  internal_code: false,
  notes: false,
};

// Column toggle UI
<DropdownMenu>
  <DropdownTrigger><Button variant="outline" size="sm">Columns</Button></DropdownTrigger>
  <DropdownContent>
    {allColumns.map(col => (
      <DropdownCheckboxItem
        key={col.id}
        checked={visibility[col.id]}
        onCheckedChange={(v) => setVisibility({...visibility, [col.id]: v})}
      >
        {col.label}
      </DropdownCheckboxItem>
    ))}
  </DropdownContent>
</DropdownMenu>
```

---

## Pattern 4: Collapse Navigation

**Problem:** Sidebar with 15+ items. User hunts instead of navigating.

**Solution:** Group into 3-5 categories. Collapse non-current groups.

### Filament Panel Provider
```php
// BEFORE: flat navigation
->navigation([
    NavigationItem::make('Dashboard'),
    NavigationItem::make('Orders'),
    NavigationItem::make('Products'),
    NavigationItem::make('Customers'),
    NavigationItem::make('Invoices'),
    NavigationItem::make('Reports'),
    NavigationItem::make('Shipments'),
    NavigationItem::make('Returns'),
    NavigationItem::make('Inventory'),
    NavigationItem::make('Settings'),
    NavigationItem::make('Users'),
    NavigationItem::make('Roles'),
    // ... more
])

// AFTER: grouped (Filament handles groups natively)
// In Resources, set:
protected static ?string $navigationGroup = 'Sales';     // Orders, Invoices
protected static ?string $navigationGroup = 'Catalog';    // Products, Inventory
protected static ?string $navigationGroup = 'Operations'; // Shipments, Returns
protected static ?string $navigationGroup = 'Settings';   // Users, Roles, Config

// In PanelProvider, collapse non-primary groups:
->collapsibleNavigationGroups(true)
```

### Generic sidebar (Blade/React/Vue)
```html
<!-- Pattern: collapsible groups with active state -->
<nav>
    <div x-data="{ open: true }">
        <button @click="open = !open" class="flex items-center justify-between w-full px-3 py-2 font-medium">
            Sales
            <svg :class="open && 'rotate-180'" class="w-4 h-4 transition-transform">...</svg>
        </button>
        <div x-show="open" class="ml-3 space-y-1">
            <a href="/orders" class="block px-3 py-1.5 text-sm">Orders</a>
            <a href="/invoices" class="block px-3 py-1.5 text-sm">Invoices</a>
        </div>
    </div>
    <!-- Other groups start collapsed -->
    <div x-data="{ open: false }">
        <!-- ... -->
    </div>
</nav>
```

---

## Pattern 5: Reduce Filters

**Problem:** 8+ filters visible. Most are rarely used. Screen feels cluttered.

**Solution:** Show 2-3 primary filters. Put the rest behind "More filters".

### Filament
```php
// Primary filters — always visible
Tables\Filters\SelectFilter::make('status')
    ->options(StatusEnum::class),
Tables\Filters\Filter::make('date_range')
    ->form([
        Forms\Components\DatePicker::make('from'),
        Forms\Components\DatePicker::make('until'),
    ]),

// Move rarely-used filters to a group or remove entirely
// Consider: if a filter is used <10% of the time, it's clutter
```

### General approach
```
// Ask these questions for each filter:
// 1. How often is this filter used? (check analytics if available)
// 2. Does the user need it to complete their PRIMARY task?
// 3. Can this be replaced with search? (full-text search covers many filter cases)
//
// If < 20% usage → hide behind "More filters" or remove
// If search covers it → remove the dedicated filter
```

---

## Pattern 6: Simplify Dashboard Widgets

**Problem:** Dashboard shows 8+ widgets/metrics. Nothing stands out. "What should I look at?"

**Solution:** 3-4 key metrics at top. Secondary metrics in expandable section or separate page.

### The rule
Ask: "If the user sees this dashboard for 3 seconds, what's the ONE thing they need to know?"

That becomes the hero metric. Add 2-3 supporting metrics. Everything else goes to a dedicated analytics/reports page.

### Filament
```php
// BEFORE: 8 widgets
protected function getHeaderWidgets(): array
{
    return [
        TotalOrdersWidget::class,
        TotalRevenueWidget::class,
        PendingOrdersWidget::class,
        ShippedOrdersWidget::class,
        ReturnsWidget::class,
        TopProductsWidget::class,
        RevenueChartWidget::class,
        CustomerGrowthWidget::class,
    ];
}

// AFTER: 3 key metrics + chart
protected function getHeaderWidgets(): array
{
    return [
        TotalRevenueWidget::class,    // Hero metric
        PendingOrdersWidget::class,   // Needs action
        ShippedTodayWidget::class,    // Daily pulse
    ];
}

// Move the rest to a dedicated Reports page
// or into the body section as collapsible
protected function getFooterWidgets(): array
{
    return [
        RevenueChartWidget::class,
    ];
}
```

---

## Pattern 7: Conditional Fields

**Problem:** Form shows all possible fields regardless of context. User sees irrelevant options.

**Solution:** Show fields only when they're relevant based on prior selections.

### Filament
```php
Forms\Components\Select::make('type')
    ->options(['individual' => 'Individual', 'company' => 'Company'])
    ->live(), // triggers reactivity

Forms\Components\TextInput::make('company_name')
    ->visible(fn (Get $get) => $get('type') === 'company'),
Forms\Components\TextInput::make('tax_number')
    ->visible(fn (Get $get) => $get('type') === 'company'),
```

### Alpine.js / Livewire
```html
<select x-model="type">
    <option value="individual">Individual</option>
    <option value="company">Company</option>
</select>

<template x-if="type === 'company'">
    <div class="space-y-3">
        <input type="text" placeholder="Company Name" />
        <input type="text" placeholder="Tax Number" />
    </div>
</template>
```

---

## Pattern 8: Replace Confirmation with Undo

**Problem:** "Are you sure?" dialogs on everything. Users develop dialog blindness.

**Solution:** For non-destructive actions, do the thing and offer undo. Reserve confirmation for truly irreversible actions only.

### When to use confirmation
- Permanently deleting data (not soft delete)
- Sending emails/notifications to external users
- Financial transactions
- Revoking access

### When to use undo instead
- Archiving (reversible)
- Soft delete
- Status changes
- Moving items
- Removing from a list (can re-add)

---

## Pattern 9: Smart Defaults

**Problem:** Every field requires a decision. User must think about things they don't care about.

**Solution:** Pre-fill with sensible defaults. Let power users override.

### Signals for what the default should be
- Most commonly selected option (check your data)
- The safest/most conservative option
- The option that matches the user's previous choice
- "Today" for date fields
- Current user for "assigned to" fields

---

## Pattern 10: Visual Hierarchy Fix

**Problem:** Everything on screen has equal visual weight. Nothing guides the eye.

**Solution:** Establish clear primary/secondary/tertiary hierarchy.

### CSS pattern (works everywhere)
```css
/* Primary actions: bold, colored, prominent */
.btn-primary { @apply bg-blue-600 text-white font-medium px-4 py-2 rounded-lg; }

/* Secondary actions: outlined, subtle */
.btn-secondary { @apply border border-gray-300 text-gray-700 px-3 py-1.5 rounded-md text-sm; }

/* Tertiary actions: text-only, small */
.btn-tertiary { @apply text-gray-500 text-sm underline-offset-2 hover:underline; }

/* Rule: only ONE primary action per screen area */
/* Multiple primaries = no primaries */
```
