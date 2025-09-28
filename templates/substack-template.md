## Note Name

```html
{{url|split:"/"|nth:3|join:""|replace:"www."}} - {{published|slice:0,10}} - {{title|safe_name}}
```

## Template Triggers

```html
/^https:\/\/(\w+)\.substack\.com\/.*$/
```

## Note content

```html
{{content}}# {{selector:h1.post-title}}

_{{selector:h3.subtitle}}_

{{selectorHtml:div.available-content div.body > :is(p:not(.button-wrapper),div:not(.subscription-widget-wrap),h2,h3,h4,h5,blockquote,ol,ul)|join:" "|remove_html:("source,.image-link-expand")|markdown|replace:"/(?<=^#{1,6} .*?)\*\*/gm":""|replace:"/\n\[(\d+?)\]\(.*?\)\n\n/gm":"\n[^$1]: "|replace:"/\[(\d+?)\]\(.*?\)/gm":"[^$1]"}}
```
