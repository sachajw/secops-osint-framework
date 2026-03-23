# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A fork of the [OSINT Framework](https://osintframework.com) — a curated, interactive tree of free OSINT tools and resources. The entire dataset lives in `public/arf.json`, rendered as a D3.js collapsible tree in the browser.

## Running Locally

```bash
npm install          # installs d3 and copies d3.min.js to public/js/
npm start            # serves public/ at http://localhost:8000
```

## Data Format (`public/arf.json`)

The entire framework is a nested tree of nodes. Two node types:

- **folder**: `{ "name": "...", "type": "folder", "children": [...] }`
- **url**: `{ "name": "...", "type": "url", "url": "https://..." }`

Name suffixes indicate special behavior:
- `(T)` — tool requiring local install
- `(D)` — Google Dork
- `(R)` — requires registration
- `(M)` — URL must be manually edited (contains search term placeholder)

`null` entries are valid in children arrays (used as spacers).

## Validation

```bash
# Validate JSON is well-formed (required before any PR)
python3 -m json.tool public/arf.json > /dev/null
```

CI runs this automatically on every push/PR. The smoke test also verifies `public/css/arf.css`, `public/js/d3.v3.min.js`, `public/js/arf.js`, and `public/arf.json` all exist.

## Dead Link Tools

```bash
# Check all URLs for dead/broken links (slow — hits all external URLs)
python3 tools/link_checker.py public/arf.json > tools/link_audit_report.json

# Remove confirmed dead links from arf.json (DNS failures, connection refused, 404s)
# Leaves alone: .onion, 403, 5xx, timeouts
python3 tools/remove_dead_links.py
```

The link check CI job runs weekly (Sundays 02:00 UTC) and on direct pushes to master — it does **not** run on PRs to avoid hammering external sites during code review.

## Adding Resources

Add entries to the appropriate folder in `public/arf.json`. Resources must be publicly accessible and free to use (registration OK, but at least partial free access required). Validate JSON before submitting.
