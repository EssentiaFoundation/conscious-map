---
title: Home
---

# Visual map

{% capture mindmap %}{% include_relative mindmap.mmd %}{% endcapture %}
<div class="mermaid">
{{ mindmap | strip }}
</div>

# Relations

{% capture relmap %}{% include_relative relations.mmd %}{% endcapture %}
<div class="mermaid">
{{ relmap | strip }}
</div>

# Browse theories

Visit the [theories index]({{ '/theories/' | relative_url }}) to browse per-theory pages.

