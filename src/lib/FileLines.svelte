<script>
    export let lines = [];
    export let width = 800;

    import * as d3 from 'd3';

    let svg;
    let files = [];
    const rowHeight = 30;

    $: files = d3.groups(lines, d => d.file)
                 .map(([name, lines]) => ({ name, lines }));

    $: if (svg && files.length) {
      const height = files.length * rowHeight;

      const svgSel = d3.select(svg)
        .attr('width', width)
        .attr('height', height);

      const groups = svgSel.selectAll('g.file')
        .data(files, d => d.name);

      groups.exit().remove();

      const enterGroups = groups.enter()
        .append('g')
        .attr('class', 'file')
        .attr('transform', (d, i) => `translate(0, ${i * rowHeight})`);

      enterGroups.append('text')
        .attr('class', 'filename')
        .attr('x', 10)
        .attr('y', rowHeight / 2)
        .attr('dominant-baseline', 'hanging')
        .text(d => d.name);

      enterGroups.append('text')
        .attr('class', 'linecount')
        .attr('x', 250)
        .attr('y', rowHeight / 2)
        .attr('dominant-baseline', 'hanging')
        .text(d => `${d.lines.length} lines`);

      groups.attr('transform', (d, i) => `translate(0, ${i * rowHeight})`)
        .select('text.filename')
        .text(d => d.name);

      groups.select('text.linecount')
        .text(d => `${d.lines.length} lines`);
    }
  </script>

  <svg bind:this={svg}></svg>


<style>
:global(text.filename) {
    font-family: 'monospace';
    font-size: 14px;
    fill: #333;
  }

:global(text.linecount) {
    font-family: sans-serif;
    font-size: 12px;
    fill: #666;
  }
</style>
