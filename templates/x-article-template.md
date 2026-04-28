## Note Name

```html
{% set note_author = selector:article[data-testid="tweet"] div[data-testid="User-Name"] span span|first|trim %}
{% set note_handle = selector:article[data-testid="tweet"] div[data-testid="User-Name"] a[tabindex="-1"] span|first|replace:"/^@/":""|trim %}
{% set note_url_handle = "" %}
{% if url contains "/status/" %}
{% set note_url_handle = url|replace:"/^https:\/\/x\.com\/([^\/]+)\/status\/\d+.*$/":"$1"|trim %}
{% endif %}
{% set note_name_author = note_author ?? note_handle ?? note_url_handle ?? author|split:", "|first ?? "Untitled" %}
{% set note_date = selector:article[data-testid="tweet"] time?datetime|first|date:("YYYY-MM-DD","YYYY-MM-DDTHH:mm:ss.SSS[Z]") ?? published|slice:0,10 ?? date|date:"YYYY-MM-DD" %}
{% set note_title = selector:div[data-testid="twitter-article-title"]|first ?? title ?? "Untitled" %}
{{note_name_author|safe_name}} - {{note_date}} - {{note_title|safe_name}}
```

## Template Triggers

```regex
/^https:\/\/x\.com\/[^\/]+\/status\/\d+/
```

## Note content

```html
# {{selector:div[data-testid="twitter-article-title"]}}

_By {{selector:article[data-testid="tweet"] div[data-testid="User-Name"] span span|first}} ({{selector:article[data-testid="tweet"] div[data-testid="User-Name"] a[tabindex="-1"] span|first}})_

[View on X]({{url}})

> **Posted:** {{selector:article[data-testid="tweet"] time?datetime|first|date:("YYYY-MM-DD HH:mm","YYYY-MM-DDTHH:mm:ss.SSS[Z]")}} ({{selector:article[data-testid="tweet"] time|first}})
> **Captured:** {{time|date:"YYYY-MM-DD HH:mm"}}

---

{{content|replace:"/([^\r\n])(?:\r?\n){2,}\s*(\[@[^\]\r\n]+\]\([^\)\r\n]+\))/g":"$1 $2"|replace:"/\)\s+([,.;:!?])/g":")$1"|replace:"/([^\r\n]*[-:])\s+(\[https?:\/\/[^\]\r\n]+\]\(https?:\/\/[^\)\r\n]+\))/g":"$1\n\n$2"|replace:"/(\[https?:\/\/[^\]\r\n]+\]\(https?:\/\/[^\)\r\n]+\))\s+(?=(?:\[@[^\]\r\n]+\]\([^\)\r\n]+\)|[A-Z]))/g":"$1\n\n"|replace:"/\)\s+(PS\b)/g":")\n\n$1"|replace:"/\[\s*(?:\r?\n)+(!\[[^\r\n]+\]\([^\)\r\n]+\))(?:\r?\n)+\]\(\/[^\)\r\n]+\/article\/\d+\/media\/\d+[^\)\r\n]*\)/g":"$1"|replace:"/\[\s*(?:\r?\n)+(!\[[^\r\n]+\]\([^\)\r\n]+\))(?:\r?\n)+\]\(https?:\/\/x\.com\/[^\)\r\n]+\/article\/\d+\/media\/\d+[^\)\r\n]*\)/g":"$1"|replace:"/(?:\r?\n){3,}/g":"\n\n"}}
```
