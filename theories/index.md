---
title: Theories
---

# Theories

Below is an automatically generated list of per-theory pages (from files under `theories/`).

<ul>
{% assign pages = site.pages | where_exp: "p", "p.url contains '/theories/'" | sort: "name" %}
{% for p in pages %}
  {% unless p.url == '/theories/' or p.name == 'index.md' %}
  <li>
    <a href="{{ p.url | relative_url }}">{{ p.name | default: p.title | default: p.url }}</a>
    {% if p.primaryCategoryId %}
      <small> — {{ p.primaryCategoryId }}</small>
    {% endif %}
  </li>
  {% endunless %}
{% endfor %}
</ul>

