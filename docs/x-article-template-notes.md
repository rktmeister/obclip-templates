---
summary: Gotchas and fixtures for maintaining templates/x-article-template.md
read_when:
  - Updating the X article template
  - Debugging broken quotes/links/images in X article captures
---

# X Article Template Notes

These are the gotchas we uncovered while tuning `templates/x-article-template.md` so future updates don’t have to rediscover them.

## Current approach

- The active template now uses `{{content}}` as the body source of truth, then applies a short Markdown-only cleanup pass.
- We intentionally retired the heavier `selectorHtml:...|markdown|replace...` pipeline from the live template after repeated browser/runtime compatibility failures in real captures. In practice, one unsupported regex was enough to leave the entire cleanup chain unapplied.
- The current cleanup pass only fixes the issues that keep showing up in live clips:
  - inline `@mentions` that get split into their own paragraphs
  - punctuation / `PS` spacing artifacts caused by rejoining those mentions
  - bare-URL citation links that X flattens into surrounding prose
  - article image wrappers that survive as `[ ![Image](...) ](/.../article/.../media/...)`
- Keep the compat cleanup narrow. We intentionally do **not** rejoin standalone raw-URL paragraphs, because that makes long source links much harder to read and also tends to collapse nearby attribution/`PS` paragraphs into one dense block.
- Note naming now uses template logic with explicit `set` variables. For X clips, the display-name selector can be empty at note-name evaluation time even though it works later in the body, so the template falls back through handle selectors and finally the status-URL handle before giving up.
- The historical notes below are still useful if we ever revive the HTML-based path, but they no longer describe the current happy path.

## DOM structure highlights

- Longform bodies live under `div[data-testid="longformRichTextComponent"]`. All article paragraphs, tweets, and media blocks are rendered as Draft.js blocks inside this container.
- Some X articles include multiple `div[data-testid="longformRichTextComponent"]` nodes (e.g., “Sources” blocks at the end). When that happens, `selectorHtml:` returns an array, so you must `|join:""` (or `|first`) before running `remove_html`, `replace`, and `markdown` filters; otherwise you’ll get JSON artifacts like `\["` / `\"` and broken image/link URLs.
- Some X articles include LaTeX rendered via KaTeX/MathML in `div[data-testid="tex-block"]` (and potentially inline variants). The underlying HTML stores both a MathML representation (`<mrow>…</mrow>`) and the original TeX in `<annotation encoding="application/x-tex">…</annotation>`. The `|markdown` conversion will otherwise concatenate both, producing garbage like `701⋅mileshour⋅160 hours=…` before the real LaTeX. The template strips everything inside `<semantics>` except the TeX annotation before running `|markdown`.
- The “hero” tweet for the article uses `article[data-testid="tweet"][tabindex="-1"]`. Embedded tweets inside the story use the same `data-testid` but have `tabindex="0"` and are wrapped in `div[data-testid="simpleTweet"]`.
- Tweet text for embeds is always under `div[data-testid="tweetText"]`; any quoted tweet laid out inside the same block nests another `article[data-testid="tweet"][tabindex="0"]`.

## Detecting embedded tweets

- Do **not** rely only on `data-testid="tweet"`; combine it with `tabindex="0"` to target embedded tweets while ignoring the primary article header.
- The regex in the template extracts display name (`span span`), handle (from the `href="/{handle}/status/…"`) and the time link (`<time datetime="…"`) in one pass. Keep it in sync if Twitter/X changes their attribute layout.

## Formatting decisions

- Embedded tweets are rewritten into Markdown blockquotes with the structure
  `> [Name (@handle)](profile) - [time text](tweet link)` followed by the tweet body.
- Tweet media (images) sits immediately after the tweet text in the HTML. We wrap the remaining HTML in `<div class="tweet-media">…</div>` **inside** the blockquote before the Markdown conversion so the image renders beneath the quote.
- X wraps images in anchor tags pointing at `/.../photo/1`. After wrapping the tweet, strip those anchors (both relative and absolute) so the Markdown engine doesn’t emit extra `[ … ]( … )` link wrappers around the `![]()` image.
- Some WebKit-based clipping contexts can emit `<img src="Image" srcset="https://pbs.twimg.com/media/...">` for longform media. Before running `|markdown`, promote the first `pbs.twimg.com` URL from `srcset` into `src` so images still render as Markdown images.
- In those same captures, malformed linked-image Markdown can surface as nested bracket forms (for example `![[Image]([Image))` wrapped in an outer link). Normalize that token, then downgrade non-URL inner images to plain links while preserving the outer `href` target.
- Helium can also preserve article media wrappers (`.../article/<id>/media/<id>`) around otherwise valid `![Image](https://pbs.twimg.com/...)` images. Strip those anchors before Markdown and keep a post-Markdown unwrapping guard keyed to `/article/.../media/...` so the final output matches Zen.
- Helium can also split inline `@mentions` into their own Markdown paragraphs even when they are semantically inline with the surrounding sentence. After `|markdown`, join only the mention-style links, then reattach any trailing punctuation and split `PS` back out when needed; this fixes cases like `one recent one by` / `[@handle](...)` / `, who…` without collapsing standalone source links into dense paragraphs.
- X can also flatten bare-URL citation links into surrounding prose even when the HTML contains soft breaks around the source URL, attribution, and `PS` sentence. The compat template now handles that structurally: split before a bare-URL Markdown link when it is introduced by a dash/colon clause, split after the bare-URL link when the next token looks like a new sentence or inline mention attribution, and split `PS` back out when it follows a Markdown link.
- Stick to standard JavaScript regex syntax in `replace` filters. Inline PCRE-style flags such as `(?i)` are invalid per the official docs and can make exported/imported templates behave unpredictably in the real extension runtime. Prefer `/.../gi`, and avoid lookbehind entirely when a capture-based rewrite is easy because some extension/browser combinations appear to stop applying later `replace` filters once they hit an unsupported lookbehind pattern.
- Post-Markdown cleanup should tolerate both `\n` and `\r\n`. The saved Markdown you inspect on disk may be normalized to LF even when the in-memory string inside the extension still uses CRLF, which can make newline-sensitive cleanup rules look correct in local testing while failing in live captures.
- Some longform images include a “caption” that starts with a URL (often a source/citation). The HTML for that caption is rendered as a separate Draft.js block, which becomes a standalone `[]()` link right after the image in Markdown. The template converts these `![Image](...) + [https://…](https://…)` sequences into Obsidian-style footnotes keyed by the image filename/media id (e.g. `[^img-G-zPg9_XYAAGEp-]`) and formats the footnote content as a proper Markdown link so it’s clickable. For nicer Live Preview editing, the footnote definition is emitted immediately after the image+marker (before any remaining caption text).
- Engagement links (`…/status/<id>/analytics`, `/likes`, etc.) and “Show more” buttons need to be removed; otherwise they leak into the Markdown output.
- Quote tweets embedded inside another tweet render as a `div` with a `Quote` label. The template now rewrites that block into a nested `.tweet-embed quote` blockquote using the embedded author name, handle, captured `<time>` text, and `data-testid="tweetText"` contents so the quote displays with the correct indentation and without avatar images. We intentionally drop the literal “Quote” string before Markdown so the nested blockquote opens directly with the quoted tweet metadata.
    - X still omits a direct status link for these quotes, so the template links to the author profile and preserves the rendered time text instead of a tweet permalink.
    - Flatten the avatar container (`div[data-testid="Tweet-User-Avatar"]`) inside the quote block before Markdown so it doesn’t surface as an extra image.
- Mentions (`@handle`) sometimes sit inside their own wrapper `<div class="... r-xoduu5 ...">`. We strip that wrapper so the link stays inline and the Markdown renderer doesn’t force the handle onto its own line.
- After the `markdown` filter runs, run a targeted cleanup that re-adds `>` prefixes to any image lines that immediately follow a tweet blockquote. X occasionally inserts extra wrapper `<div>`s (for mentions or multi-image carousels) which cause the Markdown conversion to drop the blockquote marker; the guard runs multiple passes so back-to-back images stay inside the quoted block.

## Testing tips

- Use the saved fixtures `temp/twitter-article-3.html` (single body component), `temp/twitter-article-5.html` (multiple `longformRichTextComponent` blocks / sources), and `temp/twitter-article-6.html` (KaTeX/LaTeX math blocks) to smoke-test replacements. Running a small BeautifulSoup script to apply the same regex replacements from the template helps catch formatting regressions (e.g., leftover brackets around images).
- When copying new selectors or filters, double check that the `replace` expressions are escaped for the template engine (e.g., backslashes in `\d` have to be doubled).

## Future changes checklist

1. Confirm X hasn’t changed the embedded tweet wrapper (`tabindex="0"`). If it has, update the detection regex first.
2. Re-run the HTML fixture through the regex replacements and verify:
    - Embedded tweets render as blockquotes.
    - Images remain inside the blockquote without link wrappers.
    - Non-tweet article paragraphs remain untouched.
3. Only then edit the template; the chained `replace` calls are order-sensitive, so keep the media unwrapping replacements ahead of the Markdown conversion.
