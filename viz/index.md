---
layout: default
title: Visual Map
---

# Zoomable Circle Packing

<p>Click to zoom into a category. Click a theory to open its page. Use your browser back button to return.</p>

<div id="viz-root" style="height: 70vh; border: 1px solid #eee; background: #fff;"></div>

<link rel="stylesheet" href="{{ '/assets/viz.css' | relative_url }}">
<script src="https://cdn.jsdelivr.net/npm/d3@7"></script>
<script type="module">
const dataUrl = "{{ '/data/theories.json' | relative_url }}";
const theoryBase = "{{ '/theories/' | relative_url }}";

const rootEl = document.getElementById('viz-root');
const width = rootEl.clientWidth;
const height = rootEl.clientHeight;
const svg = d3.select(rootEl).append('svg').attr('viewBox', [0, 0, width, height]).attr('width', '100%').attr('height', '100%');

fetch(dataUrl)
  .then(r => { if (!r.ok) throw new Error(`Failed to load data: ${r.status}`); return r.json(); })
  .then(model => {
    const categories = model.categories || [];
    const theories = model.theories || [];
    const catById = new Map(categories.map(c => [c.id, c]));

    // Build hierarchical data: root -> categories -> theories
    const children = categories.map(c => ({
      id: c.id,
      name: c.label,
      color: c.color || '#999',
      children: theories
        .filter(t => (t.primaryCategoryId || (t.categoryIds && t.categoryIds[0]) || 'others') === c.id)
        .map(t => ({ id: t.id, name: t.name, value: 1, color: (catById.get(c.id) || {}).color }))
    }));

    // Include an "Others" bucket if any theory lacks a known category
    const others = theories.filter(t => !catById.has(t.primaryCategoryId || (t.categoryIds && t.categoryIds[0]) || 'others'));
    if (others.length) {
      children.push({ id: 'others', name: 'Others', color: '#9ca3af', children: others.map(t => ({ id: t.id, name: t.name, value: 1 })) });
    }

    const data = { name: 'Consciousness Theories', children };

    const root = d3.pack()
      .size([width, height])
      .padding(4)
      (d3.hierarchy(data).sum(d => d.value || 1));

    const color = d => d.data.color || (d.parent ? color(d.parent) : '#999');

    let focus = root;
    const nodes = root.descendants();

    const circle = svg.append('g')
      .selectAll('circle')
      .data(nodes)
      .join('circle')
        .attr('class', d => d.children ? 'node category' : 'node theory')
        .attr('fill', d => d.children ? color(d) : color(d))
        .attr('pointer-events', d => d === root ? 'none' : null)
        .on('click', (event, d) => {
          if (d.children) {
            zoomToNode(d);
          } else if (d.data.id) {
            window.location.href = theoryBase + d.data.id + '/';
          }
          event.stopPropagation();
        })
        .on('mouseover', function() { d3.select(this).attr('stroke', '#000'); })
        .on('mouseout', function() { d3.select(this).attr('stroke', null); });

    const label = svg.append('g')
      .style('font', '12px sans-serif')
      .attr('text-anchor', 'middle')
      .selectAll('text')
      .data(nodes)
      .join('text')
        .style('fill-opacity', d => d.parent === root ? 1 : 0)
        .style('display', d => d.parent === root ? null : 'none')
        .text(d => d.data.name);

    svg.on('click', () => zoomToNode(root));

    const zoomTo = (v) => {
      const k = width / (v[2] * 2);
      const translateX = width / 2 - v[0] * k;
      const translateY = height / 2 - v[1] * k;
      svg.selectAll('g').attr('transform', `translate(${translateX},${translateY}) scale(${k})`);
    };

    const zoomToNode = (d) => {
      focus = d;
      const transition = svg.transition().duration(750);
      const v = [d.x, d.y, d.r];
      const k = width / (d.r * 2);
      const tx = width / 2 - d.x * k;
      const ty = height / 2 - d.y * k;
      svg.selectAll('g').transition(transition).attr('transform', `translate(${tx},${ty}) scale(${k})`);

      label
        .transition(transition)
        .style('fill-opacity', e => e.parent === d ? 1 : 0)
        .on('start', function(e) { if (e.parent === d) this.style.display = 'block'; })
        .on('end', function(e) { if (e.parent !== d) this.style.display = 'none'; });
    };

    // Initial layout
    svg.selectAll('g').remove();
    const g = svg.append('g');
    g.selectAll('circle').data(nodes).join('circle')
      .attr('class', d => d.children ? 'node category' : 'node theory')
      .attr('fill', d => d.children ? color(d) : color(d))
      .attr('cx', d => d.x)
      .attr('cy', d => d.y)
      .attr('r', d => d.r)
      .attr('pointer-events', d => d === root ? 'none' : null)
      .on('click', (event, d) => {
        if (d.children) zoomToNode(d); else if (d.data.id) window.location.href = theoryBase + d.data.id + '/';
        event.stopPropagation();
      });

    g.selectAll('text').data(nodes).join('text')
      .attr('x', d => d.x)
      .attr('y', d => d.y)
      .style('font', '12px sans-serif')
      .attr('text-anchor', 'middle')
      .style('fill-opacity', d => d.parent === root ? 1 : 0)
      .style('display', d => d.parent === root ? null : 'none')
      .text(d => d.data.name);

    zoomTo([root.x, root.y, root.r]);
  })
  .catch(err => {
    console.error('Circle pack load error:', err);
    const msg = document.createElement('p');
    msg.textContent = 'Failed to load visualization data.';
    rootEl.appendChild(msg);
  });
</script>

