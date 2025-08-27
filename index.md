---
title: Home
---

# Visual map

{% capture mindmap %}{% include_relative mindmap.mmd %}{% endcapture %}
<div class="mermaid">
{{ mindmap | strip }}
</div>

# Relations

{% capture relmd %}{% include_relative relation.md %}{% endcapture %}
<div class="mermaid">
{{ relmd | replace: '```mermaid','' | replace: '```','' | strip }}
</div>

# Browse theories

Visit the [theories index]({{ '/theories/' | relative_url }}) to browse per-theory pages.

