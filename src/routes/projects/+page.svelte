<script>
    import * as d3 from 'd3';
  let query = "";
  $: filteredProjects = projects.filter(project => {
	let values = Object.values(project).join("\n").toLowerCase();
	return values.includes(query.toLowerCase());
});

let pieData;

    $: {
        pieData = {};
        let rolledData = d3.rollups(filteredProjects, v => v.length, d => d.year);
        pieData = rolledData.map(([year, count]) => {
            return { value: count, label: year };
        });
    }

    import Project from "$lib/Project.svelte";
    import projects from "$lib/projects.json";
    import Pie from '$lib/Pie.svelte';

    let selectedYearIndex = -1;
    let selectedYear;
$: selectedYear = selectedYearIndex > -1 ? pieData[selectedYearIndex].label : null;
$: filteredByYear = filteredProjects.filter(project => {
        if (selectedYear) {
            return project.year === selectedYear;
        }

        return true;
    });


</script>

<Pie data={pieData} bind:selectedIndex={selectedYearIndex} />


<input type="search" bind:value={query}
       aria-label="Search projects" placeholder="🔍 Search projects…" />


<svelte:head>
  <title>Projects</title>
</svelte:head>
        <h1>{projects.length} Projects</h1>

        <div class="projects">
            {#each filteredByYear as p}

            <Project data={p}/>

            {/each}

        </div>

        <style>
          input{
            width: 100%;
          }
        </style>
