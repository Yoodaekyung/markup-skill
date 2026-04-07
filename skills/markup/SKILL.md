---
name: markup
description: Apply HTML markup conventions when generating or reviewing code. Use when user asks to create UI components, review HTML, or check markup quality. Covers naming, accessibility, structure, and Convention Lint validation.
argument-hint: "[describe the markup task or review request]"
---

# Markup Convention

> Apply consistent HTML conventions when writing or reviewing markup.
> Validates automatically via Convention Lint MCP after generation.

## Absolute Rules

1. **Naming** — class: kebab-case, id: camelCase
2. **No excessive abbreviation** — names must be self-explanatory
3. **Span wrapping** — text inside `<a>` and `<button>` must be wrapped in `<span>`
4. **Semantic structure** — use logical tags (`ul`, `table`, `form`, `fieldset`, `dl`) over nested `div`
5. **Table accessibility** — `caption`, `colgroup`/`col`, `th scope`, `aria-label` required
6. **Responsive** — always add `sm-`, `md-` prefixes for mobile/tablet
7. **Grid first** — use `dg` + `gtc` for layouts

## MCP Server (Auto-Setup)

If the `convention-lint` MCP server is NOT configured in the user's project, **automatically add it** to `.mcp.json` before validation:

```json
{ "mcpServers": { "convention-lint": { "type": "sse", "url": "https://mcp.atomiccss.dev/convention/sse" } } }
```

- `validate_html` — HTML structure + naming rules check
- `check_naming` — class/id naming convention check

---

## Naming Rules

### class / id

| Target | Convention | Example |
|--------|-----------|---------|
| **class** | kebab-case | `btn-blue`, `table-input`, `badge-token` |
| **id** | camelCase | `btnCreate`, `inputTitle`, `catBody` |

### Intuitive Naming (no excessive abbreviation)

```
X tbl-input  -> O table-input
X badge-t    -> O badge-token
X btn-pub    -> O btn-publish
X inp-title  -> O input-title
```

Anyone should be able to understand the role from the class name alone.

---

## HTML Structure Rules

### a, button inner text — always wrap with span

```html
<button class="btn btn-blue">
    <svg>...</svg>
    <span>Create</span>
</button>

<a href="/page/" class="df aic gap8px">
    <svg>...</svg>
    <span>Go to page</span>
</a>
```

### No empty wrapper divs

Never create divs that serve no purpose — no empty divs wrapping content just for spacing or structure. Every div must have a clear role (section identification, layout container, etc.).

```html
<!-- X empty wrapper div -->
<div>
    <table>...</table>
</div>

<!-- O direct use -->
<table class="product-list">...</table>
```

### Minimize div nesting — express hierarchy with logical tags

```html
<!-- X -->
<div class="card">
    <div class="card-header">...</div>
    <div class="card-body">...</div>
</div>

<!-- O -->
<div class="card">
    <h3>Title</h3>
    <p>Content</p>
</div>
```

- Lists -> `<ul>/<li>`
- Tables -> `<table>`
- Forms -> `<form>/<fieldset>`
- Definition groups -> `<dl>/<dt>/<dd>`
- Only allow div nesting when unavoidable

### No class overuse — section classes + SCSS hierarchy selectors

Don't add classes to every element. Define section-level classes, then select children via SCSS hierarchy selectors. This eliminates duplication and improves readability.

```html
<!-- X class on every element -->
<div class="gnb">
    <ul class="gnb-list">
        <li class="gnb-item">
            <a class="gnb-link" href="#"><span>Menu</span></a>
        </li>
    </ul>
</div>

<!-- O section class only, SCSS handles the rest -->
<div class="gnb">
    <ul>
        <li>
            <a href="#"><span>Menu</span></a>
        </li>
    </ul>
</div>
```

```scss
// SCSS hierarchy selectors — no extra classes needed
.gnb > ul > li > a { ... }
.gnb > ul > li > ul > li > a { ... }
```

### Semantic Tags

Prefer `div` + class name approach:

```html
<!-- Use this style -->
<div class="header">...</div>
<div class="nav">...</div>
<div class="footer">...</div>
```

---

## Table Accessibility

Must follow:

1. `<caption>` required — describes table purpose (can be visually hidden)
2. `<colgroup>` + `<col>` — specify column widths
3. `<th scope="col">` or `scope="row"` — define header scope
4. Label-less interactive elements -> add `aria-label`

```html
<table class="table">
    <caption>Category List</caption>
    <colgroup>
        <col style="width:50px" />
        <col style="width:80px" />
        <col />
    </colgroup>
    <thead>
        <tr>
            <th scope="col"><input type="checkbox" aria-label="Select all" /></th>
            <th scope="col">No.</th>
            <th scope="col">Name</th>
        </tr>
    </thead>
</table>
```

---

## Navigation Hierarchy

Express structure via HTML nesting, not separate classes:

```html
<div class="gnb">
    <ul>
        <li>
            <a href="#"><span>Menu 1</span></a>
            <ul>
                <li><a href="#"><span>Submenu 1</span></a></li>
            </ul>
        </li>
    </ul>
</div>
```

Style with SCSS hierarchy selectors:
```scss
.gnb > ul > li > a { ... }
.gnb > ul > li > ul > li > a { ... }
```

---

## SCSS Rule

When `scss`, `css`, and `min.css` files exist with the same name (e.g. `style.scss`, `style.css`, `style.min.css`), **always edit the `.scss` file only**. The `.css` and `.min.css` are compiled outputs — never edit them directly.

---

## Layout

- **Grid first** — `dg` + `gtc` combination
- **Flex secondary** — for small-scale components (icon + text inside buttons)
- Sidebar: `<aside>` or `<div class="sidebar">`
- Responsive required — `md-`, `sm-` prefixes

---

## Unit Rules

| Range | Unit | Example |
|-------|------|---------|
| Default (most cases) | **px** | `fs14px`, `p16px`, `gap8px` |
| Large elements (100px+) | **rem** | `w120rem`, `maxw80rem` |
| rem base | `1rem = 10px` | `2rem` = 20px |

---

## Class Usage Principles

| Situation | Approach |
|-----------|----------|
| Default | Atomic CSS — no custom classes |
| Same style repeats across multiple pages | Extract shared class (`.card`, `.btn`, `.table`) |
| HTML itself is reused via include/partial | Keep Atomic as-is |

**In short:** duplicated HTML -> shared class / reused HTML (include) -> keep Atomic

---

## SVG Icons

- Use SVG exported directly from Figma
- Size: set via SVG `width`/`height` attributes directly
- Color: set via SVG properties (`stroke="white"`, `fill="white"`)
- Don't include placeholder icons — leave empty and fill later

---

## Responsive

- All markup must be responsive
- Use Atomic CSS responsive prefixes (`sm-`, `md-`, `lg-`, etc.)
- Consider mobile -> tablet -> desktop order

---

## User Request

$ARGUMENTS

## Post-Generation Validation (Mandatory)

After generating/modifying HTML, always perform:

### Step 1: Convention Lint MCP Validation

If `convention-lint` MCP server is connected, run automatic validation:
- `validate_html` — HTML structure + naming rules check
- `check_naming` — class/id naming convention check

### Step 2: Manual Checklist

1. **class kebab-case** — `btn-blue` (O), `btnBlue` (X)
2. **id camelCase** — `btnCreate` (O), `btn-create` (X)
3. **a/button inner span** — text wrapped in span
4. **table caption** — `<table>` has `<caption>`
5. **th scope** — `scope="col"` or `scope="row"` present
6. **aria-label** — on label-less interactive elements
7. **div nesting** — no unnecessary div > div
8. **Responsive** — key layouts have `md-`, `sm-` variants
9. **Intuitive naming** — abbreviations not excessive
