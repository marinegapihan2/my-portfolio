<script>
    export let lines = [];
    export let width;

    import * as d3 from 'd3';

    let svg;
    // let files = [];
    // const rowHeight = 30;
    // const height = files.length * rowHeight;
  let files = [];
  $: files = d3.groups(lines, d => d.file)
                 .map(([name, lines]) => ({ name, lines }));


  $: if (svg) {
    const rowHeight = 30;
    const width = 400;
    const height = files.length * rowHeight;
    const baseY = 20;
    const totalLinesOffset = 18;


    d3.select(svg)
      .attr('width', width)
      .attr('height', height);

    const groups = d3.select(svg)
    .selectAll('g.file')
    .data(files, d => d.name);

    groups.exit().remove();

    const enterGroups = groups.enter()
        .append('g')
        .attr('class', 'file')
        .attr('transform', (d, i) => `translate(0, ${i * rowHeight})`);


    enterGroups.append('text')
        .attr('class', 'filename')
        .attr('x', 10)
        .attr('y', baseY)
        .attr('dominant-baseline', 'hanging')
        .text(d => d.name);


    enterGroups.append('text')
      .attr('class', 'linecount')
      .attr('x', 10)
      .attr('y', baseY + totalLinesOffset)
      .attr('dominant-baseline', 'hanging')
      .text(d => `${d.lines.length} lines`);


    groups.attr('transform', (d, i) => `translate(0, )`)
      .select('text.filename')
      .text(d => d.name);

    groups.select('text.linecount')
      .text(d => `${d.lines.length} lines`)
      .attr('x', 250);
  }

  function generateDots(file, svgWidth) {
  const totalDots = Math.ceil(file.lines.length / linesPerDot);
  const availableWidth = svgWidth - dotsColumnX;
  const maxDotsPerRow = Math.floor(availableWidth / approxDotWidth) || totalDots;
  let tspans = "";
  const dotRows = Math.ceil(totalDots / maxDotsPerRow);
  for (let r = 0; r < dotRows; r++) {
    const count = Math.min(maxDotsPerRow, totalDots - r * maxDotsPerRow);
    const rowDots = Array(count).fill('•').join('');
    tspans += `<tspan x="${dotsColumnX}" dy="${r === 0 ? 0 : dotRowHeight + 'px'}">${rowDots}</tspan>`;
  }
  return tspans;

}
  $: filesWithHeights = files.map(file => {
  const totalDots = Math.ceil(file.lines.length / linesPerDot);
  const availableWidth = width - dotsColumnX;
  const maxDotsPerRow = Math.floor(availableWidth / approxDotWidth) || totalDots;
  const dotRows = Math.ceil(totalDots / maxDotsPerRow);
  return { ...file, groupHeight: fileInfoHeight + dotRows * dotRowHeight };
});

$: positions = (() => {
  let pos = [], y = 0;
  for (const f of filesWithHeights) {
    pos.push(y);
    y += f.groupHeight;
  }
  return pos;
})();


  </script>

<svg bind:this={svg}></svg>

<!-- <svg viewBox="-50 -50 100 100"></svg> -->

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
