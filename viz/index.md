---
layout: default
title: Visual Map
---

# Interactive Visual Map

<p>This graph shows categories as large circles and theories as smaller circles. Theories connect to their primary category; cross-theory relations (compatibility/opposition/equivalence) are drawn between theory nodes.</p>

<div id="viz-root" style="height: 70vh; border: 1px solid #eee; background: #fff;"></div>

<div class="controls">
  <label><input type="checkbox" id="toggle-relations" checked> Show cross-theory relations</label>
</div>

<link rel="stylesheet" href="{{ '/assets/viz.css' | relative_url }}">
<script src="https://cdn.jsdelivr.net/npm/d3@7"></script>
<script type="module" src="{{ '/assets/viz.js' | relative_url }}"></script>

