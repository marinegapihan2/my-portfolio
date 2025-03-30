<script>
  import Project from "$lib/Project.svelte";
  import projects from "$lib/projects.json";
  import { onMount } from "svelte";

let githubData = null;
let loading = true;
let error = null;

onMount(async () => {
	try {
		const response = await fetch("https://api.github.com/users/marinegapihan2");
		githubData = await response.json();
	} catch (err) {
		error = err;
	}
	loading = false;
});

</script>

<svelte:head>
  <title>Marine Gapihan</title>
</svelte:head>

    <h1>Marine</h1>
    <p>Marine is a MCP1 student at MIT, originally from France. Not used to the snow, she returned to Cambridge without any snow apparel and fell to the ground two minutes after taking this picture :D </p>
    <img src="images/snow.jpeg" alt= "enjoying the snow from Hayden library">

    {#if loading}
    <p>Loading...</p>
{:else if error}
    <p class="error">Something went wrong: {error.message}</p>
{:else}
    <section>
        <h2>My GitHub Stats</h2>
        <dl>
            <dt>Followers</dt>
            <dd>{githubData.followers}</dd>
            <dt>Following</dt>
            <dd>{githubData.following}</dd>
            <dt>Public Repositories</dt>
            <dd>{githubData.public_repos}</dd>
        </dl>
    </section>
{/if}


<br>
    <h2>Latest Projects</h2>

    <div class="projects">
      {#each projects.slice (0,3) as p}

      <Project data={p} hLevel="3"/>

      {/each}

  </div>
