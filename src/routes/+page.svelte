<script>
  import Project from "$lib/Project.svelte";
  import projects from "$lib/projects.json";
</script>

<svelte:head>
  <title>Marine Gapihan</title>
</svelte:head>

    <h1>Marine</h1>
    <p>Marine is a MCP1 student at MIT, originally from France. Not used to the snow, she returned to Cambridge without any snow apparel and fell to the ground two minutes after taking this picture :D </p>
    <img src="images/snow.jpeg" alt= "enjoying the snow from Hayden library">

    <h2>Latest Projects</h2>

    <div class="projects">
      {#each projects.slice (0,3) as p}

      <Project data={p} hLevel="3"/>

      {/each}

  </div>
