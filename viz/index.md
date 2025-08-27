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
<script type="module">
// Inline module to avoid asset path/caching issues on Pages
const dataUrl = "{{ '/data/theories.json' | relative_url }}";
const rootEl = document.getElementById('viz-root');
const width = rootEl.clientWidth;
const height = rootEl.clientHeight;
const svg = d3.select(rootEl).append('svg');
const g = svg.append('g');
svg.call(d3.zoom().scaleExtent([0.2, 4]).on('zoom', (ev) => g.attr('transform', ev.transform)));
const tooltip = d3.select('body').append('div').attr('class', 'tooltip');
fetch(dataUrl).then(r => { if (!r.ok) throw new Error(`Failed to load data: ${r.status}`); return r.json(); }).then(model => {
  const categories = model.categories || [];
  const theories = model.theories || [];
  const relations = model.relations || [];
  const catById = new Map(categories.map(c => [c.id, c]));
  const R = Math.min(width, height) * 0.36; const cx = width/2, cy = height/2; const anchors = new Map();
  categories.forEach((c,i)=>{ const a=(i/categories.length)*2*Math.PI - Math.PI/2; anchors.set(c.id,{x:cx+R*Math.cos(a), y:cy+R*Math.sin(a)}); });
  const nodes=[];
  categories.forEach(c=> nodes.push({ id:`cat:${c.id}`, label:c.label, type:'category', color:c.color||'#888', r:50, fx:anchors.get(c.id).x, fy:anchors.get(c.id).y, catId:c.id }));
  theories.forEach(t=>{ const primary=t.primaryCategoryId || (t.categoryIds&&t.categoryIds[0]) || 'others'; const color=(catById.get(primary)&&catById.get(primary).color)||'#999'; nodes.push({ id:t.id, label:t.name, type:'theory', color, r:8+Math.min(8, (t.aliases?t.aliases.length:0)), catId:primary }); });
  const links=[]; theories.forEach(t=>{ const primary=t.primaryCategoryId || (t.categoryIds&&t.categoryIds[0]) || 'others'; links.push({ source:t.id, target:`cat:${primary}`, type:'belongs_to' }); });
  relations.forEach(r=> links.push({ source:r.source, target:r.target, type:r.type||'relation' }));
  const nodeById = new Map(nodes.map(n=>[n.id,n])); const validLinks = links.filter(l=> nodeById.has(l.source)&&nodeById.has(l.target));
  const sim=d3.forceSimulation(nodes)
    .force('link', d3.forceLink(validLinks).id(d=>d.id).distance(l=> l.type==='belongs_to'?40:140).strength(l=> l.type==='belongs_to'?0.4:0.05))
    .force('charge', d3.forceManyBody().strength(d=> d.type==='category'?-400:-25))
    .force('collide', d3.forceCollide().radius(d=> d.r+3))
    .force('x', d3.forceX().x(d=> d.type==='theory'?anchors.get(d.catId).x:d.fx||cx).strength(d=> d.type==='theory'?0.08:0))
    .force('y', d3.forceY().y(d=> d.type==='theory'?anchors.get(d.catId).y:d.fy||cy).strength(d=> d.type==='theory'?0.08:0));
  const link=g.append('g').attr('stroke-linecap','round').selectAll('line').data(validLinks).join('line').attr('class',d=>`link ${d.type==='belongs_to'?'belongs':'relation '+d.type}`).attr('stroke-width',d=> d.type==='belongs_to'?1.2:1.6);
  const node=g.selectAll('.node').data(nodes).join('g').attr('class',d=>`node ${d.type}`)
    .call(d3.drag().on('start',(ev,d)=>{ if(!ev.active) sim.alphaTarget(0.3).restart(); d.fx=d.x; d.fy=d.y; })
      .on('drag',(ev,d)=>{ d.fx=ev.x; d.fy=ev.y; })
      .on('end',(ev,d)=>{ if(!ev.active) sim.alphaTarget(0); if(d.type==='theory'){ d.fx=null; d.fy=null; } }))
    .on('mouseover',(ev,d)=> tooltip.style('opacity',1).text(d.label))
    .on('mousemove',(ev)=> tooltip.style('left',(ev.pageX+8)+'px').style('top',(ev.pageY+8)+'px'))
    .on('mouseout',()=> tooltip.style('opacity',0));
  node.append('circle').attr('r',d=>d.r).attr('fill',d=>d.color);
  node.filter(d=>d.type==='category').append('text').attr('dy',0).text(d=>d.label);
  const theoryLabels=node.filter(d=>d.type==='theory').append('text').attr('dy',d=>-(d.r+3)).style('display','none').text(d=>d.label);
  const toggle=document.getElementById('toggle-relations'); const update=()=>{ const show=toggle.checked; link.filter(d=>d.type!=='belongs_to').attr('display',show?null:'none'); }; toggle.addEventListener('change',update); update();
  sim.on('tick',()=>{ link.attr('x1',d=>d.source.x).attr('y1',d=>d.source.y).attr('x2',d=>d.target.x).attr('y2',d=>d.target.y); node.attr('transform',d=>`translate(${d.x},${d.y})`); });
  svg.on('mousemove.zoomcheck',()=>{ const k=d3.zoomTransform(svg.node()).k; theoryLabels.style('display', k>1.8?null:'none'); });
}).catch(err => {
  console.error('Viz load error:', err);
  const msg = document.createElement('p');
  msg.textContent = 'Failed to load visualization data.';
  rootEl.appendChild(msg);
});
</script>

