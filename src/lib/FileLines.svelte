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



  // $: if (svg) {
  //     const svgWidth = 1200;
  //     const totalHeight = positions.length
  //       ? positions[positions.length - 1] + filesWithHeights[filesWithHeights.length - 1].groupHeight
  //       : 0;
  //     d3.select(svg)
  //       .attr('width', svgWidth)
  //       .attr('height', totalHeight)
  //       .style('overflow', 'visible');

  //     // Append <defs> for drop shadow (white glow) if not already present.
  //     const defs = d3.select(svg).select("defs");
  //     if (defs.empty()) {
  //       d3.select(svg)
  //         .append("defs")
  //         .html(`<filter id="white-glow" x="-50%" y="-50%" width="200%" height="200%">
  //           <feDropShadow dx="0" dy="0" stdDeviation="2" flood-color="hsl(0, 0%, 100%)" flood-opacity="0.9"/>
  //         </filter>`);
  //     }

  //     const groups = d3.select(svg)
  //       .selectAll('g.file')
  //       .data(filesWithHeights, d => d.name);

  //     groups.exit().remove();

  //     const enterGroups = groups.enter()
  //       .append('g')
  //       .attr('class', 'file')
  //       .attr('transform', (d, i) => `translate(0, ${positions[i]})`);

  //     // Insert a background rectangle as the first child to serve as the container's background.
  //     enterGroups.insert('rect', ':first-child')
  //       .attr('class', 'file-bg')
  //       .attr('x', 0)
  //       .attr('y', 0)
  //       .attr('width', svgWidth)
  //       .attr('height', d => d.groupHeight)
  //       .style('fill', 'hsla(0, 0%, 100%, 0.9)')
  //       .style('filter', 'url(#white-glow)');

  //     // Append file name text.
  //     enterGroups.append('text')
  //       .attr('class', 'filename')
  //       .attr('x', 10)
  //       .attr('y', baseY)
  //       .attr('dominant-baseline', 'hanging')
  //       .text(d => d.name);

  //     // Append total lines text.
  //     enterGroups.append('text')
  //       .attr('class', 'total-lines')
  //       .attr('x', 10)
  //       .attr('y', baseY + totalLinesOffset)
  //       .attr('dominant-baseline', 'hanging')
  //       .text(d => `Total: ${d.lines.length} lines`);

  //     // Append unit dots text using generateDots.
  //     enterGroups.append('text')
  //       .attr('class', 'unit-dots')
  //       .attr('x', dotsColumnX)
  //       .attr('y', baseY - 2)
  //       .attr('dominant-baseline', 'mathematical')
  //       .attr('fill', "#1f77b4")
  //       .html(d => generateDots(d, svgWidth));
  //   }

  $: if (svg) {
    const rowHeight = 30;
    const width = 400;
    const height = files.length * rowHeight;

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
