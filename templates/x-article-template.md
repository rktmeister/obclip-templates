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

{{selectorHtml:div[data-testid="longformRichTextComponent"]|remove_html:("[role='status'][aria-live='polite']")|replace:"/<span[^>]*style=\"[^\"]*font-weight:\s*bold[^\"]*\"[^>]*>\s*<span[^>]*>([\s\S]*?)<\/span>\s*<\/span>/g":"<strong>$1</strong>"|replace:"/<span[^>]*style=\"[^\"]*font-weight:\s*bold[^\"]*\"[^>]*>([\s\S]*?)<\/span>/g":"<strong>$1</strong>"|replace:"/(?i)<span[^>]*style=\"[^\"]*font-style:\s*italic[^\"]*\"[^>]*>\s*<span[^>]*>([\s\S]*?)<\/span>\s*<\/span>/g":"__ITALIC__$1__ENDITALIC__"|replace:"/(?i)<span[^>]*style=\"[^\"]*font-style:\s*italic[^\"]*\"[^>]*>([\s\S]*?)<\/span>/g":"__ITALIC__$1__ENDITALIC__"|markdown|replace:"/__ITALIC__([\s\S]*?)__ENDITALIC__/g":"*$1*"|replace:"/^(#{1,6})\s*\n+([^#\n][^\n]*)/gm":"$1 $2\n"|replace:"/^(#{1,6})\s+\*\*(.*?)\*\*/gm":"$1 $2"|replace:"/(\S)\n\s*((?<!\!)\[[^\]\n]+\]\([^\)\n]+\))/g":"$1 $2"|replace:"/(?<!\!)(\[[^\]\n]+\]\([^\)\n]+\))\n\s*([.,;:!?])/g":"$1$2"|replace:"/(?<!\!)(\[[^\]\n]+\]\([^\)\n]+\))\n\s*(?=[^\s>\-#])/g":"$1 "|replace:"/^\t+/gm":""|replace:"/\[\s*!\[(.*?)\]\((.*?)\)\s*\]\([^\)]*\)/g":"![$1]($2)"|replace:"/\n{3,}/g":"\n\n"}}
```
