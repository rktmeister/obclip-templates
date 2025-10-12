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
- Quote tweets embedded inside another tweet render as a `div` with a `Quote` label. Convert that `div` into its own `.tweet-embed.quote` blockquote (using the inner `data-testid="User-Name"`, handle text, `<time>`, and `data-testid="tweetText"`) so the nested tweet shows up cleanly and without avatar images.  
  - The HTML for the nested quote is missing a direct status link, so you need to reuse the parent tweet’s handle/time instead of the usual `<a href="/handle/status/...">` selector.  
  - Flatten the avatar container (`div[data-testid="Tweet-User-Avatar"]`) inside the quote block before Markdown so it doesn’t surface as an extra image.

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
