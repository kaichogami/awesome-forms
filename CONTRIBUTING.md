# Contributing to Awesome Forms

Thanks for your interest in adding a template!

## How to Add a Template

1. Create a new directory under `templates/` with a kebab-case slug (e.g., `my-cool-form`)
2. Add two files:
   - `index.html` — your form template
   - `meta.json` — metadata about the template

### index.html Requirements

- **Self-contained** — all CSS must be inline (no external stylesheets)
- **No JavaScript required** — the form should work as a static HTML form
- **No external dependencies** — no CDNs, no fonts from Google, no frameworks
- **Responsive** — must look good on mobile (test at 360px width)
- **Accessible** — use proper labels, semantic HTML, sufficient color contrast
- **Form action** — set to `action="#"` (users replace with their own endpoint)

Add a comment block at the top of the HTML with the template name and brief usage instructions.

### meta.json Format

```json
{
  "name": "My Cool Form",
  "slug": "my-cool-form",
  "description": "A one-line description of the design style",
  "category": "contact",
  "tags": ["tag1", "tag2", "tag3"],
  "sutrena": {
    "templateId": "contact",
    "fields": [
      { "name": "email", "label": "Email", "type": "email", "required": true }
    ],
    "createDashboard": true
  }
}
```

**Fields:**
- `name` — human-readable template name
- `slug` — must match the directory name
- `description` — one line, describes the visual style
- `category` — one of: waitlist, contact, feedback, newsletter, survey, poll, rsvp, booking, order
- `tags` — 2-4 style descriptors
- `sutrena.templateId` — (optional) maps to a Sutrena built-in template
- `sutrena.fields` — field definitions matching the HTML form inputs
- `sutrena.createDashboard` — whether to auto-create a dashboard

### Field Types

Supported types: `text`, `email`, `textarea`, `number`, `select`, `checkbox`, `url`, `tel`, `date`

For `select` fields, include an `options` array: `["Option A", "Option B"]`

## Submitting

1. Fork the repo
2. Create your template directory and files
3. Test that the HTML opens correctly in a browser
4. Submit a pull request with a screenshot
