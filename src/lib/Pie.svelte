<script>
	import * as d3 from 'd3';
	import "../styles/style.css";

	let arcGenerator = d3.arc().innerRadius(0).outerRadius(50);
	export let data = [];

	let sliceGenerator = d3.pie().value(d => d.value);
	let colors = d3.scaleOrdinal(d3.schemeTableau10);

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
		liveText = `${d.label}: ${d.value} projects selected.`;
	}
}

	function handleKeydown(event, index) {
	  if (event.key === "Enter" || event.key === " ") {
		event.preventDefault(); // prevent scrolling with spacebar
		toggleWedge(index);
	  }
	}
  </script>

  <div class="container">
	<svg
  viewBox="-50 -50 100 100"
  role="img"
  aria-labelledby="pie-title pie-desc">
  <title id="pie-title">Projects by Year</title>
  <desc id="pie-desc">{description}</desc>

	  {#each arcs as arc, index}
		<g
		  tabindex="0"
		  role="button"
		  aria-label={data[index]?.label}
		  on:click={() => toggleWedge(index)}
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
  opacity: 1;
  stroke: black; /* Add a visible stroke to show focus */
  stroke-width: 2px;
}

.sr-only {
  position: absolute;
  left: -9999px;
  width: 1px;
  height: 1px;
  overflow: hidden;
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

</style>
