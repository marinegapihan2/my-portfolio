<script>
    import Project from "$lib/Project.svelte";
    import projects from "$lib/projects.json";
    import Pie from '$lib/Pie.svelte';

</script>


<svelte:head>
  <title>Projects</title>
</svelte:head>

        <h1>{projects.length} Projects</h1>

        <Pie></Pie>

        <div class="projects">
            {#each projects as p}

            <Project data={p}/>

            {/each}

        </div>
