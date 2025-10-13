# X Article Template Notes

These are the gotchas we uncovered while tuning `templates/x-article-template.md` so future updates don’t have to rediscover them.

## DOM structure highlights

- Longform bodies live under `div[data-testid="longformRichTextComponent"]`. All article paragraphs, tweets, and media blocks are rendered as Draft.js blocks inside this container.
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
- Engagement links (`…/status/<id>/analytics`, `/likes`, etc.) and “Show more” buttons need to be removed; otherwise they leak into the Markdown output.
- Quote tweets embedded inside another tweet render as a `div` with a `Quote` label. The template now rewrites that block into a nested `.tweet-embed quote` blockquote using the embedded author name, handle, captured `<time>` text, and `data-testid="tweetText"` contents so the quote displays with the correct indentation and without avatar images. We intentionally drop the literal “Quote” string before Markdown so the nested blockquote opens directly with the quoted tweet metadata.
    - X still omits a direct status link for these quotes, so the template links to the author profile and preserves the rendered time text instead of a tweet permalink.
    - Flatten the avatar container (`div[data-testid="Tweet-User-Avatar"]`) inside the quote block before Markdown so it doesn’t surface as an extra image.
- Mentions (`@handle`) sometimes sit inside their own wrapper `<div class="... r-xoduu5 ...">`. We strip that wrapper so the link stays inline and the Markdown renderer doesn’t force the handle onto its own line.
- After the `markdown` filter runs, run a targeted cleanup that re-adds `>` prefixes to any image lines that immediately follow a tweet blockquote. X occasionally inserts extra wrapper `<div>`s (for mentions or multi-image carousels) which cause the Markdown conversion to drop the blockquote marker; the guard runs multiple passes so back-to-back images stay inside the quoted block.

## Testing tips

- Use the saved fixture `temp/twitter-article-3.html` to smoke-test replacements. Running a small BeautifulSoup script to apply the same regex replacements from the template helps catch formatting regressions (e.g., leftover brackets around images).
- When copying new selectors or filters, double check that the `replace` expressions are escaped for the template engine (e.g., backslashes in `\d` have to be doubled).

## Future changes checklist

1. Confirm X hasn’t changed the embedded tweet wrapper (`tabindex="0"`). If it has, update the detection regex first.
2. Re-run the HTML fixture through the regex replacements and verify:
    - Embedded tweets render as blockquotes.
    - Images remain inside the blockquote without link wrappers.
    - Non-tweet article paragraphs remain untouched.
3. Only then edit the template; the chained `replace` calls are order-sensitive, so keep the media unwrapping replacements ahead of the Markdown conversion.
