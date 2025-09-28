# Agent Playbook

This repository houses helpers for building Obsidian Web Clipper templates. Follow this
playbook whenever you (the agent) assist a user.

Official Obsidian documentation you may need:
- https://help.obsidian.md/web-clipper/capture
- https://help.obsidian.md/web-clipper/templates
- https://help.obsidian.md/web-clipper/variables
- https://help.obsidian.md/web-clipper/filters

## 1. Understand the Request
- Confirm the target website or content source the user wants to clip.
- Ask the user for the HTML snippet or DOM structure that represents the content they want.
  Without it, you cannot build or validate selectors.
- Prefer receiving the HTML as a file within the repository (e.g., `temp/page.html`) so you
  can read it directly. This avoids truncation issues from pasted snippets.
- Clarify any site-specific behaviors (pagination, paywalls, dynamic blocks) that may affect
  clipping.
- Skim the relevant Obsidian documentation (links above) if you need to verify capture
  limitations, template syntax, variables, or filters.

## 2. Gather Required Inputs
- **Note Name**: Always aim for `author - date - title`.
  - Inspect the provided HTML/metadata yourself to locate author, publication date, and
    title nodes; derive selectors or filters without asking the user for specifics.
  - If a component is missing from the markup, surface the gap and agree on an acceptable
    fallback before continuing.
- **Template Trigger**: Ask the user for the canonical URL or pattern the template should
  match, then translate it into an appropriate regex or literal trigger. Consult the
  templates documentation if you need guidance on trigger formats.
- **Content Rules**: Default to capturing all readable text and images in a clean Markdown
  flow. Confirm only if the user explicitly mentions custom trimming, rearranging, or
  annotations. Review filter and variable references when you need to reach for advanced
  processing.

## 3. Build the Template
- Base new work on the examples in `templates/substack-template.md` and
  `templates/x-article-template.md`.
- Use the standard heading layout:
  1. `## Note Name`
  2. `## Template Triggers`
  3. `## Note content`
- Wrap Liquid expressions or regex in fenced code blocks with the correct language hint
  (`html` or `regex`).
- Favor reusable filters (`safe_name`, `markdown`, `remove_html`, etc.) to keep the output
  tidy.
- When the user provides selectors, verify them against the supplied HTML. Adjust only after
  discussing any changes.

## 4. Validate Before Delivering
- Dry-run selectors mentally (or with provided HTML) to ensure each required field resolves.
- Double-check the note name format, trigger pattern, and overall Markdown structure.
- Ensure the content block preserves headings, paragraphs, lists, quotes, and images unless
  the user explicitly wants otherwise.
- Cross-check the final template with the documentation when you rely on specialized
  variables or filters.

## 5. Collaboration Tips
- Echo assumptions back to the user before locking in a template.
- Offer concise rationale for non-obvious filters or replacements.
- If you need to search the codebase for patterns, prefer `ast-grep` with the appropriate
  language setting; fall back to `rg` only when a structural query is unnecessary.
- Respect repository constraints (e.g., do not open `.env`).

## 6. Deliverables
Provide the user with:
- The completed template file (usually named `templates/<site>-template.md`).
- A short summary of what the template captures, along with any prerequisites the user must
  satisfy (like ensuring certain DOM elements are present).
- Optional sanity-check instructions the user can follow to verify the template on their
  side.
- Optional pointers back to the relevant Obsidian docs so the user can review variable and
  filter behavior if they customize further.

Refer back to this guide whenever you work in this project so future agents maintain a
consistent, high-quality experience for users.
