<svelte:head>
  <title>Meta</title>
</svelte:head>

<script>
import * as d3 from "d3";
import { onMount } from "svelte";

import {
	computePosition,
	autoPlacement,
	offset,
} from '@floating-ui/dom';

let commitTooltip;
let tooltipPosition = {x: 0, y: 0};

async function dotInteraction (index, evt) {
	let hoveredDot = evt.target;
	if (evt.type === "mouseenter") {
		hoveredIndex = index;
		cursor = {x: evt.x, y: evt.y};
		tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
			strategy: "fixed", // because we use position: fixed
			middleware: [
				offset(5), // spacing from tooltip to dot
				autoPlacement() // see https://floating-ui.com/docs/autoplacement
			],
		});        }
	else if (evt.type === "mouseleave") {
		hoveredIndex = -1
	}
}


let data = [];
let commits = [];
let numFiles = 0;
let mostWorkDayOfWeek = "";
let width = 1000, height = 600;
let margin = {top: 10, right: 10, bottom: 30, left: 20};
let usableArea = {
	top: margin.top,
	right: width - margin.right,
	bottom: height - margin.bottom,
	left: margin.left
};
usableArea.width = usableArea.right - usableArea.left;
usableArea.height = usableArea.bottom - usableArea.top;

let xAxis, yAxis;

let yAxisGridlines;

let hoveredIndex = -1;
$: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};

let cursor = {x: 0, y: 0};



onMount(async () => {
	data = await d3.csv("/loc.csv", row => ({
	...row,
	line: Number(row.line), // or just +row.line
	depth: Number(row.depth),
	length: Number(row.length),
	date: new Date(row.date + "T00:00" + row.timezone),
	datetime: new Date(row.datetime),
    file: row.file,
}));
numFiles = new Set(data.map(d => d.file)).size;
const dayOfWeekCount = { Sunday: 0, Monday: 0, Tuesday: 0, Wednesday: 0, Thursday: 0, Friday: 0, Saturday: 0 };
    data.forEach(d => {
      const dayOfWeek = d.datetime.toLocaleString('en-US', { weekday: 'long' });
      dayOfWeekCount[dayOfWeek]++;
    });
    mostWorkDayOfWeek = Object.keys(dayOfWeekCount).reduce((a, b) => dayOfWeekCount[a] > dayOfWeekCount[b] ? a : b);

commits = d3.groups(data, d => d.commit).map(([commit, lines]) => {
	let first = lines[0];
	let {author, date, time, timezone, datetime} = first;
	let ret = {
		id: commit,
		url: "https://github.com/vis-society/lab-7/commit/" + commit,
		author, date, time, timezone, datetime,
		hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
		totalLines: lines.length
	};

	Object.defineProperty(ret, "lines", {
		value: lines,
		configurable: true,
		writable: true,
		enumerable: false,
	});

	return ret;
});
console.log(commits);

});

$: minDate = d3.min(commits.map(d => d.date));
$: maxDate = d3.max(commits.map(d => d.date));
$: maxDatePlusOne = new Date(maxDate);
$: maxDatePlusOne.setDate(maxDatePlusOne.getDate() + 1);

$: xScale = d3.scaleTime()
              .domain([minDate, maxDatePlusOne])
              .range([usableArea.left, usableArea.right])
              .nice();

$: yScale = d3.scaleLinear()
              .domain([24, 0])
              .range([usableArea.bottom, usableArea.top]);

$: {
	d3.select(xAxis).call(d3.axisBottom(xScale));
	d3.select(yAxis).call(d3.axisLeft(yScale));
	d3.select(yAxis).call(d3.axisLeft(yScale).tickFormat(d => String(d % 24).padStart(2, "0") + ":00"));

}

$: {
	d3.select(yAxisGridlines).call(
		d3.axisLeft(yScale)
		  .tickFormat("")
		  .tickSize(-usableArea.width)
	);
}

</script>


<h1>META</h1>

<p>This page includes stats about the code of this website</p>


<section>
    <h2>Project Stats</h2>
    <dl class="stats">
        <dt>Total <abbr title="Lines of code">LOC</abbr></dt>
        <dd>{data.length}</dd>

        <dt>Commits</dt>
        <dd>{commits.length}</dd>

        <dt>Number of Files</dt>
        <dd>{numFiles}</dd>

        <dt>Most Work Done on</dt>
        <dd>{mostWorkDayOfWeek}</dd>
     </dl>
</section>

<h2>Commits by time of day</h2>
<svg viewBox="0 0 {width} {height}">
	<g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
	<g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
	<g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridlines} />
	<g class="dots">
		{#each commits as commit, index }
			<circle
				on:mouseenter={evt => dotInteraction(index, evt)}
				on:mouseleave={evt => dotInteraction(index, evt)}
				cx={ xScale(commit.datetime) }
				cy={ yScale(commit.hourFrac) }
				r="5"
				fill="steelblue"
			/>
		{/each}
		</g>

</svg>

<dl
class="info tooltip"
bind:this={commitTooltip}
style="top: {tooltipPosition.y}px; left: {tooltipPosition.x}px;"
hidden={hoveredIndex === -1}
>



		<dt>Commit</dt>
		<dd><a href="{hoveredCommit.url}" target="_blank">{hoveredCommit.id}</a></dd>

		<dt>Date</dt>
		<dd>{hoveredCommit.datetime?.toLocaleString("en", {dateStyle: "full"})}</dd>

		<dt>Time</dt>
		<dd>{hoveredCommit.datetime?.toLocaleTimeString()}</dd>

		<dt>Author</dt>
		<dd>{hoveredCommit.author}</dd>

		<dt>Lines Edited</dt>
		<dd>{hoveredCommit.totalLines}</dd>
	</dl>

<style>
	svg {
		overflow: visible;
	}
	.gridlines {
	stroke-opacity: .2;
}

dl.info {
  display: block;
  padding: 0.5em;
  border: 2px oklch(0% 0% 0);
	transition-duration: 500ms;
	transition-property: opacity, visibility;

	&[hidden]:not(:hover, :focus-within) {
		opacity: 0;
		visibility: hidden;
	}
}

dl.info dt {
  text-align: left;
  font-size: 100%;
  color: gray;
  margin-bottom: 0.3em;
}

dl.info dd {
	font-size: 80%;
  text-align: left;
  margin: 0;
  margin-bottom: 0.3em;
}


.tooltip {
  position: fixed;
  top: 1em;
  left: 1em;
  padding: 0.8em 1em;
  background-color: oklch(98% 0% 0 / 80%);
  border: 2px solid gray;
  border-radius: 8px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(5px);
  color: black;
  font-size: 0.9em;
}


circle {
	transition: 200ms;

	&:hover {
		transform: scale(1.5);
	}

	transform-origin: center;
	transform-box: fill-box;

}

</style>
