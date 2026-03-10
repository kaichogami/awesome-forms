# Contributing to Workflow Blueprints

Thanks for your interest in contributing! We accept both **workflows** (multi-primitive compositions) and **themes** (beautiful form designs).

## How to Add a Workflow

Workflows are multi-primitive compositions that solve real problems by combining pages, forms, dashboards, webhooks, analytics, entries, and folders.

1. Create a new directory under `workflows/` with a kebab-case slug (e.g., `my-workflow`)
2. Add a `workflow.json` file with the workflow definition
3. Add any HTML files referenced by the workflow (e.g., `page.html`, `entry.html`)

### workflow.json Format

```json
{
  "id": "my-workflow",
  "name": "My Workflow",
  "description": "What it does and why it's useful",
  "tags": ["tag1", "tag2"],
  "primitives": ["page", "form", "dashboard"],
  "steps": [
    {
      "type": "page",
      "ref": "my-page",
      "config": {
        "slug": "my-page",
        "title": "My Page",
        "htmlFile": "page.html"
      }
    },
    {
      "type": "form",
      "ref": "my-form",
      "config": {
        "name": "My Form",
        "fields": [
          {"name": "email", "label": "Email", "type": "email", "required": true}
        ]
      }
    }
  ]
}
```

**Fields:**
- `id` — must match the directory name
- `name` — human-readable workflow name
- `description` — one sentence explaining the workflow
- `tags` — 2-4 descriptive tags
- `primitives` — array of primitives used (page, form, dashboard, webhook, analytics, entries, folder)
- `steps` — array of resource creation steps with cross-references (`$ref:step-name`)

**Supported step types:** page, form, dashboard, webhook, analytics, folder

**Cross-references:** Use `$ref:step-name` to reference a previous step's ID (e.g., `"formId": "$ref:my-form"`)

### HTML Requirements

Any HTML files referenced in the workflow must be:
- **Self-contained** — inline CSS only (no external stylesheets)
- **No JavaScript** — static HTML only
- **No external dependencies** — no CDNs, no external fonts, no frameworks
- **Responsive** — must look good on mobile (test at 360px width)
- **Accessible** — proper labels, semantic HTML, sufficient color contrast

## How to Add a Theme

Themes are beautiful form designs that work with Sutrena's 14 form presets.

1. Create a new directory under `themes/` with a kebab-case slug (e.g., `my-theme`)
2. Add two files:
   - `index.html` — your form theme
   - `meta.json` — metadata about the theme

### index.html Requirements

- **Self-contained** — all CSS must be inline (no external stylesheets)
- **No JavaScript required** — the form should work as a static HTML form
- **No external dependencies** — no CDNs, no fonts from Google, no frameworks
- **Responsive** — must look good on mobile (test at 360px width)
- **Accessible** — use proper labels, semantic HTML, sufficient color contrast
- **Form action** — set to `action="#"` (users replace with their own endpoint)

Add a comment block at the top of the HTML with the theme name and brief usage instructions.

### meta.json Format

```json
{
  "name": "My Theme",
  "slug": "my-theme",
  "description": "A one-line description of the design style",
  "category": "contact",
  "tags": ["tag1", "tag2", "tag3"],
  "liveDemo": "https://sutrena.com/workflows/my-theme",
  "sutrena": {
    "presetId": "contact",
    "fields": [
      { "name": "email", "label": "Email", "type": "email", "required": true }
    ],
    "createDashboard": true
  }
}
```

**Fields:**
- `name` — human-readable theme name
- `slug` — must match the directory name
- `description` — one line, describes the visual style
- `category` — one of: waitlist, contact, feedback, newsletter, survey, poll, rsvp, booking, order, bug-report
- `tags` — 2-4 style descriptors
- `liveDemo` — live demo URL on Sutrena
- `sutrena.presetId` — maps to a Sutrena built-in preset (contact, feedback, bug-report, waitlist, survey, poll, rsvp, nps, quiz, newsletter, booking, client-intake, order, preorder)
- `sutrena.fields` — field definitions matching the HTML form inputs
- `sutrena.createDashboard` — whether to auto-create a dashboard

### Field Types

Supported types: `text`, `email`, `textarea`, `number`, `select`, `multiselect`, `checkbox`, `url`, `tel`, `date`, `hidden`, `file`

For `select` fields, include an `options` array: `["Option A", "Option B"]`

## Submitting

1. Fork the repo
2. Create your workflow or theme directory and files
3. Test that HTML files open correctly in a browser
4. Submit a pull request with screenshots (for themes) or a workflow description (for workflows)
