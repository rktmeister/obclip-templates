## Note Name

```html
{{selector:article[data-testid="tweet"] div[data-testid="User-Name"] span
span|first|safe_name}} - {{selector:article[data-testid="tweet"]
time?datetime|first|date:("YYYY-MM-DD","YYYY-MM-DDTHH:mm:ss.SSS[Z]")}} -
{{selector:div[data-testid="twitter-article-title"]|first|safe_name}}
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

{{selectorHtml:div[data-testid="longformRichTextComponent"]|remove_html:("[role='status'][aria-live='polite']")|replace:"/<span[^>]*>\s*Quote\s*<\/span>[\s\S]*?<div(?=[^>]*tabindex=\"0\")(?=[^>]*role=\"link\")[^>]*>[\s\S]*?data-testid=\"User-Name\"[\s\S]*?<span[^>]*>\s*<span[^>]*>([^<]+)<[\s\S]*?@([A-Za-z0-9_]+)[\s\S]*?<time[^>]*datetime=\"([^\"]+)\"[^>]*>([^<]+)<\/time>[\s\S]*?data-testid=\"tweetText\"[^>]*>([\s\S]*?)<\/div>([\s\S]*?)<\/div>/g":"<blockquote class=\"tweet-embed quote\">\n<p><a href=\"https://x.com/$2\">$1 (@$2)</a> - <time datetime=\\\"$3\\\">$4</time></p>\n<p>$5</p>\n<div class=\"tweet-media\">$6</div>\n</blockquote>"|replace:"/<article(?=[^>]*data-testid=\"tweet\")(?=[^>]*tabindex=\"0\")[^>]*>[\s\S]*?<span[^>]*>\s*<span[^>]*>([^<]+)<[\s\S]*?href=\"\/([A-Za-z0-9_]+)\/status\/([^\"]+)\"[^>]*>\s*<time[^>]*datetime=\"([^\"]+)\"[^>]*>([^<]+)<\/time>[\s\S]*?data-testid=\"tweetText\"[^>]*>([\s\S]*?)<\/div>([\s\S]*?)<\/article>/g":"<blockquote class=\"tweet-embed\">\n<p><a href=\"https://x.com/$2\">$1 (@$2)</a> - <a href=\"https://x.com/$2/status/$3\">$5</a></p>\n<p>$6</p>\n<div class=\"tweet-media\">$7</div>\n</blockquote>"|replace:"/<a[^>]*data-testid=\"tweetPhoto\"[^>]*>([\s\S]*?)<\/a>/g":"$1"|replace:"/<a[^>]*href=\"https?:\/\/x\.com\/[^\"]+\/status\/[^\"]+\/photo\/\d+\"[^>]*>([\s\S]*?)<\/a>/g":"$1"|replace:"/<a[^>]*href=\"\/[^\"]+\/status\/[^\"]+\/photo\/\d+\"[^>]*>([\s\S]*?)<\/a>/g":"$1"|replace:"/\s*<div[^>]*class=\"[^\"]*r-xoduu5[^\"]*\"[^>]*>\s*(?:<span[^>]*>\s*)?((?:<a[^>]*>[\s\S]*?<\/a>)+)\s*(?:<\/span>)?\s*<\/div>\s*/g":" $1 "|replace:"/<button[^>]*>[\s\S]*?<\/button>/g":""|replace:"/<a[^>]*href=\"\/[^\"\n]+\/status\/\d+\/(?:analytics|likes|retweets|reposts|quotes)[^\"]*\"[^>]*>[\s\S]*?<\/a>/g":""|replace:"/<a[^>]*href=\"https?:\/\/x\.com\/[^\"\n]+\/status\/\d+\/(?:analytics|likes|retweets|reposts|quotes)[^\"]*\"[^>]*>[\s\S]*?<\/a>/g":""|replace:"/<span[^>]*style=\"[^\"]*font-weight:\s*bold[^\"]*\"[^>]*>\s*<span[^>]*>([\s\S]*?)<\/span>\s*<\/span>/g":"<strong>$1</strong>"|replace:"/<span[^>]*style=\"[^\"]*font-weight:\s*bold[^\"]*\"[^>]*>([\s\S]*?)<\/span>/g":"<strong>$1</strong>"|replace:"/(?i)<span[^>]*style=\"[^\"]*font-style:\s*italic[^\"]*\"[^>]*>\s*<span[^>]*>([\s\S]*?)<\/span>\s*<\/span>/g":"__ITALIC__$1__ENDITALIC__"|replace:"/(?i)<span[^>]*style=\"[^\"]*font-style:\s*italic[^\"]*\"[^>]*>([\s\S]*?)<\/span>/g":"__ITALIC__$1__ENDITALIC__"|replace:"/(<pre[^>]*>\s*<code[^>]*class=\"[^\"]*language-([A-Za-z0-9_+#.-]+)[^\"]*\"[^>]*>)/g":"@@LANG:$2@@$1"|markdown|replace:"/(^|\n)([> ]*)@@LANG:([A-Za-z0-9_+#.-]+)@@(?:\n[> ]*)*\n([> ]*)```/gm":"$1$4```$3"|replace:"/(^|\n)([> ]*)([A-Za-z0-9_+#.-]+)(?:\n\2[ \t]*)*\n(?=\2```\3(\n|$))/gm":"$1"|replace:"/@@LANG:[A-Za-z0-9_+#.-]+@@/g":""|replace:"/__ITALIC__([\s\S]*?)__ENDITALIC__/g":"*$1*"|replace:"/^(#{1,6})\s*\n+([^#\n][^\n]*)/gm":"$1 $2\n"|replace:"/^(#{1,6})\s+\*\*(.*?)\*\*/gm":"$1 $2"|replace:"/(\S)\n\s*((?<!\!)\[[^\]\n]+\]\([^\)\n]+\))/g":"$1 $2"|replace:"/(?<!\!)(\[[^\]\n]+\]\([^\)\n]+\))\n\s*([.,;:!?])/g":"$1$2"|replace:"/(?<!\!)(\[[^\]\n]+\]\([^\)\n]+\))\n\s*(?=[^\s>\-#])/g":"$1 "|replace:"/(?<=\[@[^\]]+\]\([^\)]+\))\n>\s*\n> (?=\S)/g":" "|replace:"/^> > /gm":">> "|replace:"/^\t+/gm":""|replace:"/\[\s*!\[(.*?)\]\((.*?)\)\s*\]\([^\)]*\)/g":"![$1]($2)"|replace:"/\[(\s*!\[[^\]]*\]\([^\)]+\)\s*)\]\([^\)]+\)/g":"$1"|replace:"/(^>.*\n)\n(!\[[^\n]+\]\([^\)\n]+\))/gm":"$1> \n> $2"|replace:"/(^>.*\n)\n(!\[[^\n]+\]\([^\)\n]+\))/gm":"$1> \n> $2"|replace:"/(^>.*\n)\n(!\[[^\n]+\]\([^\)\n]+\))/gm":"$1> \n> $2"|replace:"/(^>.*\n)\n(!\[[^\n]+\]\([^\)\n]+\))/gm":"$1> \n> $2"|replace:"/\n{3,}/g":"\n\n"}}
```
