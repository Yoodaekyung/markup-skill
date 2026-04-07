# Markup Convention Skill for Claude Code

A Claude Code skill that applies consistent HTML markup conventions — naming, accessibility, structure, and automated validation via Convention Lint MCP.

## What it does

When you type `/markup`, Claude generates or reviews HTML following strict conventions:

```
/markup Create a login form with email and password fields
```

Claude will output semantic, accessible HTML following all conventions:
```html
<form class="dg gap2rem maxw40rem mxa">
    <fieldset class="dg gap16px">
        <legend>Login</legend>
        <div class="dg gap8px">
            <label for="inputEmail"><span>Email</span></label>
            <input type="email" id="inputEmail" />
        </div>
        <div class="dg gap8px">
            <label for="inputPassword"><span>Password</span></label>
            <input type="password" id="inputPassword" />
        </div>
        <button class="btn btn-blue">
            <span>Sign In</span>
        </button>
    </fieldset>
</form>
```

## Installation

### Option 1: Plugin Marketplace

```
/plugin marketplace add Yoodaekyung/markup-skill
/plugin install markup-convention@Yoodaekyung-markup-skill
```

### Option 2: Clone directly

```bash
git clone https://github.com/Yoodaekyung/markup-skill.git
cd markup-skill
# Skills are immediately available
```

### Option 3: Copy to your project

Copy `.claude/skills/markup/` to your project's `.claude/skills/` directory.

## Convention Lint MCP

The skill automatically adds the MCP server to `.mcp.json` if not configured:

```json
{ "mcpServers": { "convention-lint": { "type": "sse", "url": "https://mcp.atomiccss.dev/convention/sse" } } }
```

This gives Claude access to:
- `validate_html` — HTML structure + naming rules check
- `check_naming` — class/id naming convention check

## Usage Examples

```
/markup Login form with email and password
/markup Review this HTML for convention compliance
/markup Table with accessibility (caption, scope, aria-label)
/markup Responsive navigation with submenu
/markup Dashboard layout with sidebar and header
```

## Key Features

- **Naming conventions** — class: kebab-case, id: camelCase
- **Table accessibility** — caption, colgroup, th scope, aria-label
- **Semantic structure** — logical tags over div nesting
- **Span wrapping** — text in `<a>` and `<button>` always wrapped
- **Convention Lint** — automated MCP validation

