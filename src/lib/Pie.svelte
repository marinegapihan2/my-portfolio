<script>
	import * as d3 from 'd3';
	import "../styles/style.css";

	let arcGenerator = d3.arc().innerRadius(0).outerRadius(50);
	export let data = [];

	let sliceGenerator = d3.pie().value(d => d.value);

	$: colors = d3.scaleOrdinal()
    .domain(data.map((_, i) => i))
    .range(d3.quantize(d3.interpolateBlues, data.length));



	let arcData;
	let arcs;
	let liveText = "";


	$: {
	  arcData = sliceGenerator(data);
	  arcs = arcData.map(d => arcGenerator(d));
	}

	$: description = `A pie chart showing project counts by year. ${data.map(d => `${d.label}: ${d.value} projects`).join(', ')}.`;


	export let selectedIndex = -1;

	function toggleWedge(index, event) {
	if (!event.key || event.key === "Enter") {
		selectedIndex = index;
		const d = data[index];
		liveText = `${d.label}: ${d.value} projects selected`;
	}
}

	function handleKeydown(event, index) {
	  if (event.key === "Enter" || event.key === " ") {
		event.preventDefault(); // prevent scrolling with spacebar
		toggleWedge(index);
	  }
	}


	let showChart = true;

function toggleView() {
	showChart = !showChart;
	liveText = showChart ? "Pie chart view shown." : "Table view shown.";
}

  </script>

<button
  on:click={toggleView}
  aria-pressed={!showChart}
  aria-label="Toggle between pie chart and table view"
  class="toggle-button">
    {showChart ? 'Show Table' : 'Show Chart'}
</button>

{#if showChart}
  <div class="container">
	<svg
  viewBox="-50 -50 100 100"
  role="img"
  aria-labelledby="pie-title pie-desc">
  <title id="pie-title">Projects by Year</title>
  <desc id="pie-desc">{description}</desc>
  <circle class="pie-outline" r="50" />
	  {#each arcs as arc, index}
		<g
		  tabindex="0"
		  role="button"
		  on:click={(e) => toggleWedge(index, e)}
		  on:keydown={(e) => handleKeydown(e, index)}
		  class:selected={selectedIndex === index}
		>
		  <path
			d={arc}
			fill={colors(index)}
		  />
		</g>
	  {/each}
	</svg>

	<p aria-live="polite" class="sr-only">{liveText}</p>

	<ul class="legend">
	  {#each data as d, index}
		<li
		  style="--color: {colors(index)}"
		  class:selected={selectedIndex === index}
		  on:click={() => selectedIndex = selectedIndex === index ? -1 : index}
		>
		  <span class="swatch"></span>
		  {d.label} <em>({d.value})</em>
		</li>
	  {/each}
	</ul>
  </div>
  {:else}
  <table aria-label="Table showing project counts by year" class="data-table">
	<caption>Projects by Year</caption>
	<thead>
		<tr>
		  <th id="year-header" scope="col">Year</th>
		  <th id="projects-header" scope="col">Projects</th>
		</tr>
	  </thead>
	<tbody>
		{#each data as d, i}
		  <tr>
			<th id="row-{i}" scope="row">{d.label}</th>
			<td aria-labelledby="row-{i} projects-header">{d.value}</td>
		  </tr>
		{/each}
	  </tbody>
  </table>
  {/if}
<style>


/* Set full opacity for all segments by default */
svg path {
    transition: opacity 0.3s ease;
    opacity: 1; /* Default full opacity for all segments */
}

/* When a segment is hovered, make it fully opaque */
svg path:hover {
    opacity: 1;
}

/* When a segment is selected, make it fully opaque */
svg path.selected {
    opacity: 1 !important;
}

/* When a segment is not hovered, reduce opacity */
svg:has(path:hover) path:not(:hover) {
	opacity: 50%;
}

path {
	transition: 300ms;
	outline: none;
	cursor: pointer;
}

svg:hover path:not(:hover), svg:focus-visible path:not(:focus-visible) { opacity: 50%; }

path:hover,
path:focus-visible {
  stroke: white;
  stroke-width: 2px;
  stroke-dasharray: 4; /* Adjust the dash length as needed */
  }


.sr-only {
  position: absolute !important;
  width: 1px !important;
  height: 1px !important;
  padding: 0 !important;
  margin: -1px !important;
  overflow: hidden !important;
  clip: rect(0, 0, 0, 0) !important;
  white-space: nowrap !important;
  border: 0 !important;
}


.selected {
	--color: oklch(60% 45% 0) !important;

	&:is(path) {
		fill: var(--color) !important;
	}

	&:is(li) {
		color: var(--color);
	}
}

ul:has(.selected) li:not(.selected) {
	color: gray;
}

.legend:has(.selected) li:not(.selected) {
	color: gray;
}

.legend li.selected {
	color: oklch(60% 45% 0);
}

.legend li.selected .swatch {
	background-color: oklch(60% 45% 0) !important;
}

    svg {
	max-width: 20em;
	margin-block: 2em;

	overflow: visible;
}


.swatch {
    display: inline-block;
    width: 10px;
    height: 10px;
    background-color: var(--color);

}


	ul{
		display: grid;
		grid-template-columns: repeat(auto-fill, minmax(8em, 1fr));
		border: 0.5px solid black ;
		padding: 1%;
		margin: 1ch;

	}

	li{
	display: flex;
	align-items: center;
	gap: 0.5em;
	}

.container{
	display: flex;
	align-items: center;
    gap: 20px;
}

.legend{
	flex: 1;
}


path {
	transition: 300ms;
	outline: none;
}

.data-table {
  margin-top: 1rem;
  margin-bottom: 1rem;
  border-collapse: collapse;
  width: 100%;
  max-width: 30em;
}

.data-table caption {
  font-weight: bold;
  margin-bottom: 0.5em;
  text-align: left;
}

.data-table th,
.data-table td {
  border: 1px solid #ccc;
  padding: 0.5em;
  text-align: left;
}

.data-table th {
  background-color: #f0f0f0;
}

.pie-outline {
    stroke: black;
    fill: none;
    stroke-width: 1;
}


</style>
