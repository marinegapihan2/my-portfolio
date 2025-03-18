<script>
import * as d3 from 'd3';
import "../styles/style.css";
let arcGenerator = d3.arc().innerRadius(0).outerRadius(50);
let arc = arcGenerator({
	startAngle: 0,
	endAngle: 2 * Math.PI
});
export let data = [];

let sliceGenerator = d3.pie().value(d => d.value);
let colors = d3.scaleOrdinal(d3.schemeTableau10);

let arcData;
let arcs;

    $: {
		arcData = sliceGenerator(data);
		arcs = arcData.map(d => arcGenerator(d));
    }

export let selectedIndex = -1;

</script>


<div class="container">
	<svg viewBox="-50 -50 100 100">
		{#each arcs as arc, index}
	<path d={arc} fill={ colors(index) }
	    class:selected={selectedIndex === index}
	    on:click={e => selectedIndex = selectedIndex === index ? -1 : index}
		/>
{/each}

	</svg>

	<ul class="legend">
		{#each data as d, index}
		<li style="--color: { colors(index) }">
			<span class:selected={selectedIndex === index} class="swatch"></span>
			{d.label} <em>({d.value})</em>
		</li>
	{/each}
	</ul>
</div>

<style>

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
path:hover {
	opacity: 100% !important;
}

    svg {
	max-width: 20em;
	margin-block: 2em;

	overflow: visible;
}

svg:has(path:hover) path:not(:hover) {
	opacity: 50%;
}
path {
	transition: 300ms;
	cursor: pointer;
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
	align-items: center; /* Aligns items vertically */
    gap: 20px;
}

.legend{
	flex: 1;
}
</style>
