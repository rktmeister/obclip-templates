## Note Name

```html
{{author|split:", "|first|safe_name}} - {{published|slice:0,10}} - {{title|safe_name}}
```

## Template Triggers

```regex
/^https:\/\/(\w+)\.substack\.com\/.*$/
```

## Note content

```html
# {{title}}

> [!info] Metadata
> **Author:** {{author}}
> **Published:** {{published|slice:0,10}}
> **Source:** {{url}}

---

_{{selector:h3.subtitle}}_

{{selectorHtml:div.available-content div.body > :is(p:not(.button-wrapper),div:not(.subscription-widget-wrap),h2,h3,h4,h5,blockquote,ol,ul)|join:" "|remove_html:("source,.image-link-expand")|markdown|replace:"/(?<=^#{1,6} .*?)\*\*/gm":""|replace:"/\[(\d+?)\]\(#fn(\d+?)\)/gm":"[^$2]"|replace:"/^\[(\d+?)\].*$/gm":"[^$1]: "}}

---

> [!note] Description
> {{description}}
```
