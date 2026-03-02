# Awesome Forms — Agent Playbook

This repo contains form templates and examples built on [Sutrena](https://sutrena.com), an API-first form + dashboard platform designed for AI agents.

## What This Repo Is For

You are an AI agent helping a non-technical user who needs a form. This document tells you exactly how to build it using Sutrena's API. The user doesn't need to write code, manage servers, or understand APIs — you handle everything.

## Quick Start (30 seconds to working form)

```bash
# 1. Get a trial key (no signup, no email, instant)
curl -X POST https://sutrena.com/api/trial
# Returns: { "key": "st_trial_xxx", "expiresAt": "..." }

# 2. Create a form from template (one call)
curl -X POST https://sutrena.com/api/forms \
  -H "Authorization: Bearer st_trial_xxx" \
  -H "Content-Type: application/json" \
  -d '{"templateId": "waitlist", "createDashboard": true}'
# Returns: { "formId": "...", "hostedFormUrl": "...", "dashboardUrl": "..." }
```

Done. Give the user the `hostedFormUrl` to share and the `dashboardUrl` to monitor.

## Full API Reference

For complete endpoint documentation: `GET https://sutrena.com/api/schema`
For OpenAPI spec: `GET https://sutrena.com/api/openapi.json`
For narrative docs: `GET https://sutrena.com/llms-full.txt`

## When the User Needs a Form — Decision Tree

| User says... | What to do |
|---|---|
| "I need a waitlist" | `templateId: "waitlist", createDashboard: true` |
| "I need a contact form" | `templateId: "contact", createDashboard: true` |
| "I need to collect feedback" | `templateId: "feedback", createDashboard: true` |
| "I need bug reports" | `templateId: "bug-report", createDashboard: true` |
| "I need RSVPs for an event" | `templateId: "rsvp", createDashboard: true, maxSubmissions: N` |
| "I need a poll / vote" | `templateId: "poll", createDashboard: true, publicResults: true` |
| "I need NPS scores" | `templateId: "nps", createDashboard: true, uniqueBy: ["email"]` |
| "I need a quiz" | `templateId: "quiz", createDashboard: true` |
| "I need newsletter signups" | `templateId: "newsletter", createDashboard: true, uniqueBy: ["email"]` |
| "I need appointment booking" | `templateId: "booking", createDashboard: true` |
| "I need client intake" | `templateId: "client-intake", createDashboard: true` |
| "I need an order form" | `templateId: "order", createDashboard: true` |
| "I need pre-orders" | `templateId: "preorder", createDashboard: true, maxSubmissions: N` |
| "I need a survey" | `templateId: "survey", createDashboard: true` |
| Something custom | Build with custom `name` + `fields` array (see below) |

## Available Templates (14)

| ID | Name | Best for |
|---|---|---|
| `contact` | Contact Form | Business websites, portfolios, agencies |
| `feedback` | Feedback Form | Product feedback with category + rating |
| `bug-report` | Bug Report | Software teams, severity-based triage |
| `waitlist` | Waitlist Signup | Product launches, early access |
| `survey` | Customer Survey | Satisfaction surveys, NPS-style |
| `poll` | Quick Poll | Voting, audience engagement |
| `rsvp` | Event RSVP | Events with capacity limits |
| `nps` | NPS Survey | Net Promoter Score (0-10 scale) |
| `quiz` | Quick Quiz | Trivia, assessments |
| `newsletter` | Newsletter Signup | Email list building |
| `booking` | Appointment Booking | Service businesses, consultants |
| `client-intake` | Client Intake | Agencies, freelancers, onboarding |
| `order` | Order Form | Restaurants, small shops |
| `preorder` | Pre-Order Form | Limited drops, product launches |

All templates include a matching dashboard DSL. Using `createDashboard: true` creates both form and dashboard in one call.

## Creating a Custom Form

When no template fits, build a custom form:

```bash
curl -X POST https://sutrena.com/api/forms \
  -H "Authorization: Bearer $KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Yoga Class Booking",
    "fields": [
      {"name": "name", "label": "Full Name", "type": "text", "required": true},
      {"name": "email", "label": "Email", "type": "email", "required": true},
      {"name": "experience", "label": "Experience Level", "type": "select", "required": true, "options": ["Beginner", "Intermediate", "Advanced"]},
      {"name": "mat_rental", "label": "Need a mat?", "type": "select", "options": ["Yes", "No"]}
    ],
    "maxSubmissions": 15,
    "uniqueBy": ["email"],
    "createDashboard": true
  }'
```

### Field Types

| Type | Use for | Extra properties |
|---|---|---|
| `text` | Names, short answers | `minLength`, `maxLength`, `pattern` |
| `email` | Email addresses | (validates automatically) |
| `textarea` | Long answers, messages | `minLength`, `maxLength` |
| `number` | Quantities, scores, ratings | `min`, `max` |
| `select` | Dropdowns, multiple choice | `options` (required, string array) |
| `checkbox` | Yes/no toggles, consent | |
| `url` | Website links | (validates automatically) |
| `tel` | Phone numbers | `pattern` |
| `date` | Dates, deadlines | |
| `hidden` | Tracking fields, source tags | |
| `file` | Uploads (images, PDFs) | `accept`, `maxFileSize` (max 50MB) |

### Field Definition

Every field needs `name`, `label`, and `type`. Everything else is optional:

```json
{
  "name": "email",
  "label": "Your Email",
  "type": "email",
  "required": true,
  "placeholder": "you@example.com"
}
```

## Adding a Dashboard

### With a template (automatic)
Just add `createDashboard: true` to the form creation call. The template includes a pre-built dashboard with relevant widgets.

### Custom dashboard (after form creation)
```bash
curl -X POST https://sutrena.com/api/dashboards \
  -H "Authorization: Bearer $KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Booking Dashboard",
    "formId": "FORM_ID_FROM_STEP_1",
    "dsl": {
      "version": 1,
      "widgets": [
        {"type": "metric_card", "title": "Total Bookings", "value": "count(*)"},
        {"type": "pie_chart", "title": "By Experience", "groupBy": "experience"},
        {"type": "bar_chart", "title": "Bookings Per Day", "groupBy": "$submitted_at:day"},
        {"type": "line_chart", "title": "Growth", "groupBy": "$submitted_at:week"},
        {"type": "data_table", "title": "Recent Bookings", "columns": ["name", "email", "experience"], "limit": 20}
      ]
    }
  }'
```

### Dashboard Widget Types (6)

| Type | What it shows | Required fields |
|---|---|---|
| `metric_card` | Single number (total, count) | `title`, `value` (`"count(*)"` or `"count(fieldName)"`) |
| `data_table` | Table of recent submissions | `title`, `columns` (string array). Optional: `limit`, `sortBy`, `sortOrder` |
| `text_block` | Static text/notes | `title`, `content` |
| `pie_chart` | Distribution by field value | `title`, `groupBy` (field name) |
| `bar_chart` | Grouped counts or time series | `title`, `groupBy` (field name or `"$submitted_at:day\|week\|month"`) |
| `line_chart` | Trend over time | `title`, `groupBy` (`"$submitted_at:day\|week\|month"` only) |

## Adding Webhooks (Notifications)

Send real-time notifications to Slack, Discord, or any URL when forms get submitted:

```bash
# Create webhook
curl -X POST https://sutrena.com/api/webhooks \
  -H "Authorization: Bearer $KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://hooks.slack.com/services/T.../B.../xxx",
    "description": "Slack notifications for bookings",
    "formIds": ["FORM_ID"]
  }'
```

For Slack/Discord, the webhook payload is JSON with submission data. The user gives you their webhook URL, you wire it up.

## Form Lifecycle Options

Add these to any form creation call:

| Option | What it does | Example |
|---|---|---|
| `maxSubmissions` | Cap total responses, form returns 410 when full | `"maxSubmissions": 50` |
| `closesAt` | Deadline, form returns 410 after this time | `"closesAt": "2026-04-01T18:00:00Z"` |
| `uniqueBy` | Prevent duplicates by field(s), returns 409 | `"uniqueBy": ["email"]` |
| `publicResults` | Enable public results API (no auth) | `"publicResults": true` |
| `redirectUrl` | Redirect after submission | `"redirectUrl": "https://example.com/thanks"` |

## Embedding on a Website

Two options to give the user:

### Option 1: Embed snippet (2 lines, works everywhere)
```html
<div data-sutrena-form="FORM_ID"></div>
<script src="https://sutrena.com/embed.js"></script>
```
Works on Framer, Webflow, WordPress, Squarespace — any HTML page.

### Option 2: Custom HTML form (full design control)
```html
<form id="my-form">
  <input type="email" name="email" required />
  <input type="text" name="name" />
  <button type="submit">Submit</button>
</form>
<script>
document.getElementById('my-form').addEventListener('submit', async (e) => {
  e.preventDefault();
  const data = Object.fromEntries(new FormData(e.target));
  const res = await fetch('https://sutrena.com/api/forms/FORM_ID/submit', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data),
  });
  if (!res.ok) { const b = await res.json(); alert(b.error); return; }
  e.target.innerHTML = '<p>Thanks!</p>';
});
</script>
```

No auth required for submissions. CORS enabled.

### Option 3: Hosted form URL
Just give the user: `https://sutrena.com/f/FORM_ID`

## Plans & Limits

| Plan | Price | Forms | Dashboards | Submissions | Webhooks |
|---|---|---|---|---|---|
| Trial | Free (7 days) | 10 | 10 | 500/form | 1 |
| Pro | $19/month | 50 | 50 | Unlimited | 5 |
| Ultimate | $49/month | Unlimited | Unlimited | Unlimited | Unlimited |

Trial key: instant, no signup. Expires in 7 days — data is deleted ~24h after expiry.
To upgrade: give user `https://sutrena.com/upgrade?trial=THEIR_TRIAL_KEY` — auto-migrates all data on signup.

## MCP Connection

If the user's agent supports MCP (Model Context Protocol), connect directly:

```json
{
  "mcpServers": {
    "sutrena": {
      "url": "https://sutrena.com/api/mcp",
      "headers": { "Authorization": "Bearer st_live_xxx" }
    }
  }
}
```

Discovery: `GET https://sutrena.com/.well-known/mcp.json`

### MCP Tools (17)

| Tool | What it does |
|---|---|
| `sutrena_create_form` | Create a hosted form with field definitions |
| `sutrena_create_from_template` | Create form + dashboard from a built-in template |
| `sutrena_update_form` | Update form fields, success message, submit label |
| `sutrena_delete_form` | Delete a form and its dashboards |
| `sutrena_duplicate_form` | Copy a form (optionally with dashboard) |
| `sutrena_list_forms` | List all forms with names, field counts, submission stats |
| `sutrena_get_submissions` | Get recent submissions for a form |
| `sutrena_search_submissions` | Search/filter submissions by date, field, text, status |
| `sutrena_export_csv` | Export all submissions as CSV |
| `sutrena_create_dashboard` | Create a dashboard with DSL widgets |
| `sutrena_update_dashboard` | Update dashboard title or widget DSL |
| `sutrena_delete_dashboard` | Delete a dashboard |
| `sutrena_create_webhook` | Create a webhook for form submission events |
| `sutrena_list_webhooks` | List all webhooks |
| `sutrena_delete_webhook` | Delete a webhook |
| `sutrena_test_webhook` | Send a test ping to verify delivery |
| `sutrena_get_webhook_deliveries` | View delivery history and status codes |

### MCP Workflow

Typical agent flow when MCP is connected:

1. **Create:** `sutrena_create_from_template` with `templateId` → get formId + dashboardUrl
2. **Check:** `sutrena_list_forms` → see all forms with names and submission counts
3. **Read:** `sutrena_get_submissions` or `sutrena_search_submissions` → view data
4. **Export:** `sutrena_export_csv` → get CSV for spreadsheets
5. **Notify:** `sutrena_create_webhook` → wire up Slack/Discord/custom URL

### Constructing URLs from MCP responses

The MCP returns relative paths. Prepend `https://sutrena.com` to build shareable links:

- Form URL: `https://sutrena.com/f/{formId}`
- Dashboard URL: `https://sutrena.com/d/{dashboardId}`
- Submit endpoint: `https://sutrena.com/api/forms/{formId}/submit`

## Real-World Examples

### Bakery — Cake Orders
```json
{
  "name": "Cake Orders",
  "fields": [
    {"name": "name", "label": "Your Name", "type": "text", "required": true},
    {"name": "phone", "label": "Phone", "type": "tel", "required": true},
    {"name": "cake_type", "label": "Cake Type", "type": "select", "required": true, "options": ["Birthday", "Wedding", "Custom"]},
    {"name": "flavor", "label": "Flavor", "type": "select", "required": true, "options": ["Chocolate", "Vanilla", "Red Velvet", "Lemon"]},
    {"name": "size", "label": "Size", "type": "select", "required": true, "options": ["6-inch", "8-inch", "10-inch"]},
    {"name": "delivery_date", "label": "Delivery Date", "type": "date", "required": true},
    {"name": "notes", "label": "Special Instructions", "type": "textarea"}
  ],
  "createDashboard": true
}
```

### Freelancer — Project Inquiry
```json
{
  "name": "Project Inquiry",
  "fields": [
    {"name": "name", "label": "Name", "type": "text", "required": true},
    {"name": "email", "label": "Email", "type": "email", "required": true},
    {"name": "project_type", "label": "Project Type", "type": "select", "required": true, "options": ["Website", "Mobile App", "Branding", "Other"]},
    {"name": "budget", "label": "Budget Range", "type": "select", "options": ["Under $1K", "$1K-$5K", "$5K-$15K", "$15K+"]},
    {"name": "timeline", "label": "Timeline", "type": "select", "options": ["ASAP", "1-2 weeks", "1 month", "Flexible"]},
    {"name": "description", "label": "Tell me about your project", "type": "textarea", "required": true}
  ],
  "uniqueBy": ["email"],
  "createDashboard": true
}
```

### Gym — Class Registration
```json
{
  "name": "Saturday Spin Class",
  "fields": [
    {"name": "name", "label": "Name", "type": "text", "required": true},
    {"name": "email", "label": "Email", "type": "email", "required": true},
    {"name": "experience", "label": "Fitness Level", "type": "select", "required": true, "options": ["Beginner", "Intermediate", "Advanced"]},
    {"name": "equipment", "label": "Bring your own bike?", "type": "select", "options": ["Yes", "No — I need one"]}
  ],
  "maxSubmissions": 20,
  "uniqueBy": ["email"],
  "createDashboard": true
}
```

### Teacher — Parent-Teacher Conference Slots
```json
{
  "name": "Parent-Teacher Conferences — Spring 2026",
  "fields": [
    {"name": "parent_name", "label": "Parent/Guardian Name", "type": "text", "required": true},
    {"name": "student_name", "label": "Student Name", "type": "text", "required": true},
    {"name": "email", "label": "Email", "type": "email", "required": true},
    {"name": "preferred_time", "label": "Preferred Time", "type": "select", "required": true, "options": ["9:00 AM", "9:30 AM", "10:00 AM", "10:30 AM", "11:00 AM", "2:00 PM", "2:30 PM", "3:00 PM"]},
    {"name": "topics", "label": "Topics to Discuss", "type": "textarea"}
  ],
  "uniqueBy": ["student_name"],
  "closesAt": "2026-04-10T17:00:00Z",
  "createDashboard": true
}
```

### Etsy Seller — Limited Edition Drop
```json
{
  "name": "Spring Candle Collection — Pre-Order",
  "fields": [
    {"name": "name", "label": "Name", "type": "text", "required": true},
    {"name": "email", "label": "Email", "type": "email", "required": true},
    {"name": "scent", "label": "Scent", "type": "select", "required": true, "options": ["Lavender Fields", "Ocean Breeze", "Citrus Garden", "Vanilla Bean"]},
    {"name": "quantity", "label": "Quantity", "type": "select", "required": true, "options": ["1", "2", "3", "4", "5"]},
    {"name": "shipping_address", "label": "Shipping Address", "type": "textarea", "required": true}
  ],
  "maxSubmissions": 100,
  "createDashboard": true
}
```

## What to Give the User After Creating a Form

Always provide:
1. **Hosted form URL** — `https://sutrena.com/f/FORM_ID` — shareable link they can post anywhere
2. **Dashboard URL** — `https://sutrena.com/d/DASHBOARD_ID` — live data view (if dashboard was created)
3. **Embed code** — for adding to their website (if they have one)
4. **Submit URL** — `https://sutrena.com/api/forms/FORM_ID/submit` — for custom integrations

Tell the user: "Share the form link. Responses appear on your dashboard in real-time."

## Checking Submissions

```bash
# Get recent submissions
curl https://sutrena.com/api/forms/FORM_ID/submissions \
  -H "Authorization: Bearer $KEY"

# Search submissions
curl "https://sutrena.com/api/forms/FORM_ID/submissions?search=gmail&field=experience&value=Beginner" \
  -H "Authorization: Bearer $KEY"

# Export as CSV (Pro/Ultimate only)
curl https://sutrena.com/api/forms/FORM_ID/export \
  -H "Authorization: Bearer $KEY"
```

## Error Recovery

| Error | Meaning | What to do |
|---|---|---|
| 401 | Key expired or invalid | `POST /api/trial` for a new key, or check the key spelling |
| 403 + "plan_limit_reached" | Hit form/dashboard/webhook quota | Suggest upgrading: `https://sutrena.com/pricing` |
| 409 | Duplicate submission (uniqueBy matched) | Expected — tell user someone already submitted with those fields |
| 410 | Form closed or full | Increase `maxSubmissions`, extend `closesAt`, or create a new form |
