# Workflow Blueprints

Multi-primitive workflow compositions for AI agents on [Sutrena](https://sutrena.com). Think of these as executable playbooks that combine **pages**, **forms**, **dashboards**, **webhooks**, **analytics**, **entries**, and **folders** into end-to-end solutions.

## What's Inside

### 8 Multi-Primitive Workflows

| Workflow | Primitives | Use Case |
|----------|-----------|----------|
| [status-page](workflows/status-page) | page + form + entries + webhook | Public status page with incident reporting and team notifications |
| [changelog](workflows/changelog) | page + form + entries + dashboard | Public changelog with release notes and velocity metrics |
| [launch-kit](workflows/launch-kit) | page + form + dashboard + analytics | Complete product launch: landing page, waitlist, growth dashboard, analytics |
| [feedback-wall](workflows/feedback-wall) | page + form + entries | Public testimonial wall with auto-publishing feedback |
| [hiring-pipeline](workflows/hiring-pipeline) | form + dashboard + webhook | Job application form with interactive triage dashboard |
| [customer-research](workflows/customer-research) | folder + 3 forms + dashboard | Organized research suite: NPS, feature priority, interviews |
| [personal-crm](workflows/personal-crm) | form + dashboard + webhook + analytics | Lightweight CRM with deal pipeline and stage management |
| [community-board](workflows/community-board) | page + form + entries + dashboard | Public community board with engagement metrics |

### 10 Design Themes

Beautiful, ready-to-use form themes that work with Sutrena's 14 form presets:

| Theme | Category | Style | Demo |
|-------|----------|-------|------|
| [minimal-contact](themes/minimal-contact) | contact | Clean, whitespace-heavy | [Live Demo](https://sutrena.com/workflows/minimal-contact) |
| [gradient-survey](themes/gradient-survey) | survey | Soft gradient mesh | [Live Demo](https://sutrena.com/workflows/gradient-survey) |
| [elegant-rsvp](themes/elegant-rsvp) | rsvp | Dark with gold accents | [Live Demo](https://sutrena.com/workflows/elegant-rsvp) |
| [glassmorphism-feedback](themes/glassmorphism-feedback) | feedback | Frosted glass on gradient | [Live Demo](https://sutrena.com/workflows/glassmorphism-feedback) |
| [neo-brutalist-waitlist](themes/neo-brutalist-waitlist) | waitlist | Bold, high-contrast | [Live Demo](https://sutrena.com/workflows/neo-brutalist-waitlist) |
| [screentime-survey](themes/screentime-survey) | survey | Anonymous multi-step poll | [Live Demo](https://sutrena.com/workflows/screentime-survey) |
| [neumorphism-booking](themes/neumorphism-booking) | booking | Soft neumorphic UI | [Live Demo](https://sutrena.com/workflows/neumorphism-booking) |
| [clean-order](themes/clean-order) | order | Professional commercial | [Live Demo](https://sutrena.com/workflows/clean-order) |
| [darkmode-bugreport](themes/darkmode-bugreport) | bug-report | Developer-friendly dark | [Live Demo](https://sutrena.com/workflows/darkmode-bugreport) |
| [retro-newsletter](themes/retro-newsletter) | newsletter | 90s terminal aesthetic | [Live Demo](https://sutrena.com/workflows/retro-newsletter) |

## Usage

AI agents execute workflows via Sutrena's MCP (Model Context Protocol) server. Each workflow is a declarative JSON file that specifies resource creation steps with cross-references.

**Example:** The `status-page` workflow creates a page with an entry template, a form that auto-publishes submissions to that page, and a webhook that notifies your team on Slack when incidents are reported.

Agents parse `workflow.json`, resolve `$ref:` placeholders (e.g., `$ref:status-page` becomes the actual page ID after creation), and execute steps sequentially using Sutrena's 63 MCP tools.

See [AGENTS.md](AGENTS.md) for the complete agent playbook.

## Quick Start for Agents

1. **Get a trial account**: `POST https://sutrena.com/api/trial` → returns API key
2. **Connect via MCP**: Use the returned key to authenticate
3. **Execute a workflow**: Parse `workflow.json`, create resources in order, resolve references
4. **Share the result**: Return page URL, form URL, dashboard URL to the user

## Contributing

Contributions welcome! We accept:

- **New workflows**: Multi-primitive compositions that solve real problems
- **New themes**: Beautiful form designs (HTML + CSS only, no JS)
- **Improvements**: Better docs, bug fixes, UX enhancements

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

MIT License - see [LICENSE](LICENSE) for details.

All workflows and themes are free to use, modify, and distribute.

## Links

- [Sutrena Platform](https://sutrena.com)
- [Agent Documentation](AGENTS.md)
- [MCP Server Guide](https://sutrena.com/guides/mcp-server-forms)
- [API Reference](https://sutrena.com/api/schema)
