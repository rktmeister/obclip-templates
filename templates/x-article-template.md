## Note Name

```html
{{selector:article[data-testid="tweet"] div[data-testid="User-Name"] span span|first|safe_name}} - {{selector:article[data-testid="tweet"] time?datetime|first|date:("YYYY-MM-DD","YYYY-MM-DDTHH:mm:ss.SSS[Z]")}} - {{selector:div[data-testid="twitter-article-title"]|first|safe_name}}
```

## Template Triggers

```regex
/^https:\/\/x\.com\/(?:i\/article\/\d+|[^\/]+\/status\/\d+)/
```

## Note content

```html
# {{selector:div[data-testid="twitter-article-title"]}}

_By {{selector:article[data-testid="tweet"] div[data-testid="User-Name"] span span|first}} ({{selector:article[data-testid="tweet"] div[data-testid="User-Name"] a[tabindex="-1"] span|first}})_

[View on X]({{url}})

> **Posted:** {{selector:article[data-testid="tweet"] time?datetime|first|date:("YYYY-MM-DD HH:mm","YYYY-MM-DDTHH:mm:ss.SSS[Z]")}} ({{selector:article[data-testid="tweet"] time|first}})
> **Captured:** {{time|date:"YYYY-MM-DD HH:mm"}}

---

{{selectorHtml:div[data-testid="longformRichTextComponent"]|remove_html:("[role='status'][aria-live='polite'],div[data-testid='tweetPhoto']")|markdown|replace:"/\[\s*!\[(.*?)\]\((.*?)\)\s*\]\([^\)]*\)/g":"![$1]($2)"|replace:"/\n{3,}/g":"\n\n"}}
```
