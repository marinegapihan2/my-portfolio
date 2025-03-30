<svelte:head>
  <title>Meta</title>
</svelte:head>

<script>
import * as d3 from "d3";
import { onMount } from "svelte";

let data = [];
let commits = [];
let numFiles = 0;
let mostWorkDayOfWeek = "";


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
