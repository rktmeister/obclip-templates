# Obsidian Web Clipper Templates

This repository collects reusable templates and guidance for Obsidian's Web Clipper. Use it
to capture rich web content into Obsidian with consistent note naming, triggers, and body
formatting.

## Repository Layout
- `substack-template.md` – Example template for Substack posts.
- `x-article-template.md` – Example template for X (Twitter) Articles.
- `AGENTS.md` – Playbook followed by AI agents when assisting with new templates.

## Prerequisites
1. Obsidian Desktop or Mobile with the Web Clipper enabled.
2. Obsidian account signed in to sync the clipper (required by the official extension).
3. Obsidian Web Clipper browser extension installed and connected to your vault.
4. Familiarity with Liquid-style variables and filters used by the clipper (see References
   below).

## Using the Provided Templates
1. Open the template file you want (`substack-template.md` or `x-article-template.md`).
2. Copy the contents into a new Web Clipper template inside Obsidian (Settings → Web
   Clipper → Templates → New Template).
3. Adjust the trigger pattern if your target URLs differ.
4. Test by clipping a representative page and confirm the resulting note uses the
   `author - date - title` name format and preserves headings, text, and images.

## Requesting or Building New Templates
If you are working with an AI agent (like the one that maintains this repository):
1. Provide the agent with the target URL(s) and, ideally, the relevant HTML snippet or page
   structure so selectors can be derived accurately. For best results, save the HTML into a
   file within this repository (e.g., `temp/page.html`) rather than pasting large snippets
   into the chat.
2. Specify the trigger URL pattern you want the template to match.
3. Mention any custom formatting preferences beyond the default "preserve all readable text
   and images" behavior.
4. The agent will follow `AGENTS.md` to craft a new `<site>-template.md`. Review the result
   before adding it to your Obsidian templates.

If you are creating a template manually:
1. Start by cloning or downloading this repository.
2. Duplicate one of the existing template files and rename it to match the target site.
3. Replace the sections under `## Note Name`, `## Template Triggers`, and `## Note content`
   with selectors drawn from your page's HTML.
4. Keep the note name in the `author - date - title` format whenever possible.
5. Validate the template by clipping multiple sample URLs.

## Tips for Accurate Selectors
- Inspect the live page using your browser's developer tools to gather stable selectors.
- Prefer semantic attributes (e.g., `data-*`, `itemprop`, `aria-*`) over auto-generated class
  names.
- When content loads dynamically, capture the post-rendered HTML from the network inspector
  or fallback to static alternatives.
- Document any assumptions inside the template file so future users know what elements must
  exist.

## References
Official Web Clipper documentation:
- https://help.obsidian.md/web-clipper/capture
- https://help.obsidian.md/web-clipper/templates
- https://help.obsidian.md/web-clipper/variables
- https://help.obsidian.md/web-clipper/filters
- https://help.obsidian.md/web-clipper (overview and setup)
- https://help.obsidian.md/web-clipper/highlight
- https://help.obsidian.md/web-clipper/interpreter
- https://help.obsidian.md/web-clipper/troubleshoot

## Contributing
Contributions are welcome. When submitting a new template:
- Follow the structure demonstrated in the existing examples.
- Include testing notes or caveats in the template file.
- Keep selectors resilient and explain any fallbacks.
- If you are an AI agent, ensure `AGENTS.md` remains up to date with your workflow changes.

Happy clipping!
