# Workflow Blueprints — Agent Playbook

Complete reference for AI agents building with [Sutrena](https://sutrena.com), a web runtime designed for AI agents. This document covers all 7 primitives, 63 MCP tools, 14 form presets, 8 workflows, and 4 plan tiers.

## What is Sutrena?

Sutrena is an agent-first web runtime that provides 7 composable primitives:

1. **Pages** — Static HTML pages with optional entry templates
2. **Forms** — Data collection with 12 field types and lifecycle controls
3. **Dashboards** — Live data visualization with 7 widget types and 4 data sources
4. **Webhooks** — Real-time notifications to 5 platforms (Slack, Discord, Telegram, Teams, Google Chat)
5. **Analytics** — Privacy-first web analytics with 3 query primitives
6. **Entries** — Dynamic content injection into pages via templates
7. **Folders** — Organizational containers for resources

## Quick Start (30 seconds)

```bash
# 1. Get a trial key (instant, no signup)
curl -X POST https://sutrena.com/api/trial
# Returns: { "key": "st_trial_xxx", "claimUrl": "...", "claimDeadline": "..." }

# 2. Create a form from preset
curl -X POST https://sutrena.com/api/forms \
  -H "Authorization: Bearer st_trial_xxx" \
  -H "Content-Type: application/json" \
  -d '{"templateId": "waitlist", "createDashboard": true}'
# Returns: { "formId": "...", "hostedFormUrl": "...", "dashboardUrl": "..." }
```

Done. Give the user the URLs.

## Plans & Limits

| Plan | Price | Projects | Submissions/Form | Events/mo | Webhooks | Domains | Storage |
|------|-------|----------|------------------|-----------|----------|---------|---------|
| **Free** | $0 | 10 | 500 | 10K | 1 | 0 | 50 MB |
| **Builder** | $9/mo | 50 | 5,000 | 100K | 5 | 1 | 500 MB |
| **Pro** | $29/mo | 200 | Unlimited | 1M | 10 | 5 | 5 GB |
| **Scale** | $79/mo | Unlimited | Unlimited | Unlimited | Unlimited | Unlimited | Unlimited |

**Projects** = forms + pages + dashboards + analytics sites (single pool).
**Trial**: Free tier with 24-hour claim TTL. User gets a `claimUrl` to migrate data to permanent account.

## 7 Composable Primitives

### 1. Pages

Static HTML pages with optional dynamic entry templates.

**Key features:**
- Slug-based routing (`alice.sutrena.com/about`)
- Raw content types for robots.txt, sitemap.xml, etc.
- Entry templates with `{{placeholder}}` (escaped) or `{{{placeholder}}}` (raw) syntax
- Scheduled publishing via `publishAt`
- Shared CSS/JS across subdomain

**Common use cases:**
- Landing pages
- Status pages with live incident feed
- Changelog with auto-publishing releases
- Testimonial walls
- Community boards

### 2. Forms

Data collection with 12 field types, lifecycle controls, and auto-entry publishing.

**Field types:** text, email, textarea, number, select, multiselect, checkbox, url, tel, date, hidden, file

**Lifecycle options:**
- `maxSubmissions` — cap total responses
- `closesAt` — deadline (ISO datetime)
- `uniqueBy` — prevent duplicates by field(s)
- `redirectUrl` — post-submission redirect
- `autoEntryPageId` — publish submissions to page as entries
- `emailTo` — email notifications (Builder+)

**Conditional logic:**
- `showIf: { field: "fieldName", equals: "value" }` for conditional visibility

### 3. Dashboards

Live data visualization with 7 widget types and 4 data sources.

**7 widget types:**
1. **metric_card** — Single number (count, sum, avg, min, max)
2. **data_table** — Table of rows with columns
3. **text_block** — Static text/notes
4. **pie_chart** — Distribution by field value
5. **bar_chart** — Grouped counts or time series
6. **line_chart** — Trend over time
7. **action_table** — Interactive table with inline editable dropdowns (requires formId, private only)

**4 data sources:**
1. **Form submissions** (`formId`) — live form data
2. **Inline JSON** (`data` array, max 1000 rows) — static data
3. **CSV** (`csvObjectId`, max 10MB/100K rows) — uploaded files
4. **Analytics sites** (`analyticsSiteId`) — web analytics events

**Analytics widgets** support expressive fields: `metric` (page_views, unique_visitors, visits, bounce_rate), `breakdownBy` (url, referrer, country, device, browser, os), `period` (today, 7d, 30d, 90d), `filters`, `compare` (for period comparison).

### 4. Webhooks

Real-time notifications to 5 platforms or any HTTPS endpoint.

**Supported platforms:**
- Slack (`template: "slack"`)
- Discord (`template: "discord"`)
- Telegram (`template: "telegram"`, requires `telegramChatId`)
- Microsoft Teams (`template: "teams"`)
- Google Chat (`template: "google-chat"`)

**Field mapping:** Remap field names in webhook payload via `fieldMapping` object.

### 5. Analytics

Privacy-first web analytics — no cookies, no PII, IP geolocation via CF-IPCountry header then discarded.

**3 query primitives:**
1. **Query** — metrics (page_views, unique_visitors, visits, bounce_rate) + time series + breakdowns
2. **Funnel** — 2-5 step conversion analysis
3. **Retention** — cohort retention rates over time

**Tracking methods:**
- **External sites**: `<script defer data-site="sa_xxx" src="https://sutrena.com/a.js"></script>`
- **Sutrena-hosted pages**: Auto-collected server-side (no client JS needed)

### 6. Entries

Dynamic content injection into pages via templates. Submissions from forms with `autoEntryPageId` set auto-publish to the linked page. Max 500 entries per page.

**Template syntax:**
- `{{key}}` — HTML-escaped (safe for user input)
- `{{{key}}}` — Raw/unescaped (for pre-sanitized HTML)

**Marker:** `<!-- sutrena:entries -->` in page HTML

### 7. Folders

Lightweight organizational containers that group resources (forms, pages, dashboards, analytics sites). Unlimited on all plans. API keys can be scoped to a folder.

## 14 Form Presets

Use `templateId` in form creation for instant setup with matching dashboard.

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

All presets include a matching dashboard DSL. Using `createDashboard: true` creates both form and dashboard in one call.

## 8 Workflows

Multi-primitive compositions in this repo. Each workflow is a declarative JSON file with cross-referenced steps.

| Workflow | Primitives | Use Case |
|----------|-----------|----------|
| `status-page` | page + form + entries + webhook | Public status page with incident reporting and team notifications |
| `changelog` | page + form + entries + dashboard | Public changelog with release notes and velocity metrics |
| `launch-kit` | page + form + dashboard + analytics | Complete product launch: landing page, waitlist, growth dashboard, analytics |
| `feedback-wall` | page + form + entries | Public testimonial wall with auto-publishing feedback |
| `hiring-pipeline` | form + dashboard + webhook | Job application form with interactive triage dashboard |
| `customer-research` | folder + 3 forms + dashboard | Organized research suite: NPS, feature priority, interviews |
| `personal-crm` | form + dashboard + webhook + analytics | Lightweight CRM with deal pipeline and stage management |
| `community-board` | page + form + entries + dashboard | Public community board with engagement metrics |

See individual workflow directories for `workflow.json` definitions.

## 63 MCP Tools

Sutrena exposes 63 tools via MCP (Model Context Protocol). Organized by category:

### Forms (7 tools)
- `sutrena_create_form` — Create a hosted form with field definitions
- `sutrena_create_from_template` — Create form + dashboard from a preset
- `sutrena_update_form` — Update form fields, success message, submit label
- `sutrena_delete_form` — Delete a form and its dashboards
- `sutrena_duplicate_form` — Copy a form (optionally with dashboard)
- `sutrena_list_forms` — List all forms with names, field counts, submission stats
- `sutrena_get_form` — Get full form config by ID

### Submissions (5 tools)
- `sutrena_get_submissions` — Get recent submissions for a form
- `sutrena_search_submissions` — Search/filter submissions by date, field, text, status
- `sutrena_upsert_submission` — Insert or update a submission by externalId (Builder+)
- `sutrena_update_submission` — Update submission payload (Builder+)
- `sutrena_delete_submissions` — Delete all submissions matching an email (GDPR, Builder+)
- `sutrena_export_csv` — Export all submissions as CSV

### Dashboards (6 tools)
- `sutrena_create_dashboard` — Create a dashboard with DSL widgets
- `sutrena_get_dashboard` — Get full dashboard config by ID
- `sutrena_update_dashboard` — Update dashboard title or widget DSL
- `sutrena_delete_dashboard` — Delete a dashboard
- `sutrena_upload_dashboard_data` — Presign CSV upload for dashboard data
- `sutrena_list_dashboards` — List all dashboards (implicit, via list_forms)

### Webhooks (7 tools)
- `sutrena_create_webhook` — Create a webhook for form submission events
- `sutrena_list_webhooks` — List all webhooks
- `sutrena_get_webhook` — Get full webhook config by ID
- `sutrena_update_webhook` — Update webhook URL, events, active status, field mapping, template
- `sutrena_delete_webhook` — Delete a webhook
- `sutrena_test_webhook` — Send a test ping to verify delivery
- `sutrena_get_webhook_deliveries` — View delivery history and status codes

### Pages (6 tools)
- `sutrena_create_page` — Create a page with HTML, CSS, optional entry template
- `sutrena_list_pages` — List all pages with view counts
- `sutrena_get_page` — Get full page content by ID
- `sutrena_update_page` — Update page HTML, CSS, title, published status
- `sutrena_delete_page` — Delete a page
- `sutrena_manage_page_entries` — Add, list, or delete entries on a page

### Page Assets (3 tools)
- `sutrena_upload_page_asset` — Presign file upload for a page asset (image, video, etc.)
- `sutrena_upload_page_assets_batch` — Batch presign up to 20 asset uploads in one call
- `sutrena_list_page_assets` — List uploaded page assets
- `sutrena_delete_page_asset` — Delete an uploaded page asset

### Site Deployment (2 tools)
- `sutrena_deploy_site` — Presign a zip upload for static site deployment (Phase 1)
- `sutrena_deploy_site_process` — Extract and deploy all pages and assets from uploaded zip (Phase 2)

### Subdomains (4 tools)
- `sutrena_set_subdomain` — Set your subdomain (e.g. alice.sutrena.com)
- `sutrena_get_subdomain` — Read your current subdomain setting
- `sutrena_list_subdomains` — List all subdomains with page counts
- `sutrena_create_subdomain` — Create a new subdomain (unlimited on all plans)
- `sutrena_delete_subdomain` — Delete a subdomain and all its pages

### Custom Domains (4 tools)
- `sutrena_add_custom_domain` — Add a custom domain (e.g. mysite.com)
- `sutrena_list_custom_domains` — List custom domains with DNS/SSL status
- `sutrena_update_custom_domain` — Change which subdomain a custom domain serves from
- `sutrena_delete_custom_domain` — Remove a custom domain

### Analytics (7 tools)
- `sutrena_create_analytics_site` — Create an analytics site for tracking
- `sutrena_list_analytics_sites` — List all analytics sites with event counts
- `sutrena_delete_analytics_site` — Delete an analytics site and all events
- `sutrena_track_event` — Manually track a custom event
- `sutrena_analytics_query` — Query analytics data (metrics, time series, breakdowns)
- `sutrena_analytics_funnel` — Analyze conversion funnels (2-5 steps)
- `sutrena_analytics_retention` — Analyze user retention (cohort analysis)

### Folders (5 tools)
- `sutrena_create_folder` — Create a folder for organizing resources
- `sutrena_list_folders` — List folders with resource counts
- `sutrena_update_folder` — Update folder name or description
- `sutrena_delete_folder` — Delete a folder (resources ungrouped, not deleted)
- `sutrena_organize_folder` — Batch move resources into or out of a folder

### Account & Usage (2 tools)
- `sutrena_check_usage` — Check current usage and quotas for all resources
- `sutrena_get_account` — Read account info (plan, email, name, subscription status)
- `sutrena_submit_feedback` — Submit feedback, bug report, or feature request to the Sutrena team

## MCP Connection

### Fastest way to connect (one command, no signup)

```bash
export SUTRENA_KEY=$(curl -s -X POST https://sutrena.com/api/trial | grep -o '"key":"[^"]*"' | cut -d'"' -f4) && claude mcp add sutrena --transport streamable-http --url https://sutrena.com/api/mcp --header "Authorization: Bearer $SUTRENA_KEY"
```

7-day trial, 63 tools, no signup. Upgrade later at https://sutrena.com/pricing.

### Setup by provider

**Claude Code:**
```bash
claude mcp add sutrena --transport streamable-http \
  --url https://sutrena.com/api/mcp \
  --header "Authorization: Bearer YOUR_KEY"
```

Or add to `.mcp.json` in your project root:
```json
{
  "mcpServers": {
    "sutrena": {
      "type": "streamable-http",
      "url": "https://sutrena.com/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_KEY" }
    }
  }
}
```

**Cursor:**
Settings → MCP → Add new MCP server → Type: streamable-http, URL: `https://sutrena.com/api/mcp`, Header: `Authorization: Bearer YOUR_KEY`

**Windsurf:**
Settings → Cascade → MCP → Add server → Streamable HTTP → URL: `https://sutrena.com/api/mcp` with Bearer token header

**Codex (OpenAI):**
```bash
codex --url https://sutrena.com/api/mcp --bearer-token-env-var SUTRENA_API_KEY
```

**Any MCP client:**
POST/GET `https://sutrena.com/api/mcp` with header `Authorization: Bearer st_live_xxx` or `st_trial_xxx`

Discovery: `GET https://sutrena.com/.well-known/mcp.json`

## Decision Tree

| User says... | What to do |
|---|---|
| "I need a waitlist" | `templateId: "waitlist", createDashboard: true` |
| "I need a contact form" | `templateId: "contact", createDashboard: true` |
| "I need to collect feedback" | `templateId: "feedback", createDashboard: true` |
| "I need bug reports" | `templateId: "bug-report", createDashboard: true` |
| "I need RSVPs for an event" | `templateId: "rsvp", createDashboard: true, maxSubmissions: N` |
| "I need a poll / vote" | `templateId: "poll", createDashboard: true` |
| "I need NPS scores" | `templateId: "nps", createDashboard: true, uniqueBy: ["email"]` |
| "I need a quiz" | `templateId: "quiz", createDashboard: true` |
| "I need newsletter signups" | `templateId: "newsletter", createDashboard: true, uniqueBy: ["email"]` |
| "I need appointment booking" | `templateId: "booking", createDashboard: true` |
| "I need client intake" | `templateId: "client-intake", createDashboard: true` |
| "I need an order form" | `templateId: "order", createDashboard: true` |
| "I need pre-orders" | `templateId: "preorder", createDashboard: true, maxSubmissions: N` |
| "I need a survey" | `templateId: "survey", createDashboard: true` |
| "I need a landing page" | Execute `launch-kit` workflow |
| "I need a status page" | Execute `status-page` workflow |
| "I need a changelog" | Execute `changelog` workflow |
| "I need a testimonial wall" | Execute `feedback-wall` workflow |
| Something custom | Build with custom `name` + `fields` array |

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

## Error Recovery

| Error | Meaning | What to do |
|---|---|---|
| 401 | Key expired or invalid | `POST /api/trial` for a new key, or check the key spelling |
| 403 + "plan_limit_reached" | Hit form/dashboard/webhook quota | Suggest upgrading: `https://sutrena.com/pricing` |
| 409 | Duplicate submission (uniqueBy matched) | Expected — tell user someone already submitted with those fields |
| 410 | Form closed or full | Increase `maxSubmissions`, extend `closesAt`, or create a new form |

## What to Give the User After Creating Resources

Always provide:
1. **Hosted form URL** — `https://sutrena.com/f/{formId}` — shareable link
2. **Dashboard URL** — `https://sutrena.com/d/{dashboardId}` — live data view (if dashboard was created)
3. **Page URL** — `https://{subdomain}.sutrena.com/{slug}` — public page (if page was created)
4. **Embed code** — for adding to their website (if they have one)
5. **Submit URL** — `https://sutrena.com/api/forms/{formId}/submit` — for custom integrations

Tell the user: "Share the link. Responses appear on your dashboard in real-time."

## Full API Reference

For complete endpoint documentation: `GET https://sutrena.com/api/schema`
For OpenAPI spec: `GET https://sutrena.com/api/openapi.json`
For narrative docs: `GET https://sutrena.com/llms-full.txt`

## Links

- [Sutrena Platform](https://sutrena.com)
- [MCP Server Guide](https://sutrena.com/guides/mcp-server-forms)
- [API Reference](https://sutrena.com/api/schema)
- [Pricing](https://sutrena.com/pricing)
