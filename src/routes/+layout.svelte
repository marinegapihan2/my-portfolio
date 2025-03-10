
<script>
import { page } from "$app/stores";

let pages = [
	{url: "./", title: "Home"},
	{url: "projects", title: "Projects"},
    {url: "contact", title: "Contact"},
    {url: "CV", title: "Resume"},
    {url: "https://github.com/marinegapihan2", title: "GitHub" }
];


</script>


<nav>

    {#each pages as p}
    <a
    href={p.url}
    class:current={$page.route.id === p.url}
    target={p.url.startsWith("http") ? "_blank" : null}
  >
    {p.title}
  </a>
  {/each}
</nav>

  <style>

  nav ul,
nav li {
    display: contents;
}

nav {
  display: flex;
  }
  nav a {
    flex: 1;
    text-decoration: none ;
    color: inherit;
    text-align: center;
    padding: 1em;
    margin-bottom: 3ch ;
    border-bottom-width: 1px;
    border-bottom-style: solid;
    border-color: oklch(50% 10% 200 / 40%);
  }

  nav a.current {
    border-bottom: 0.4em solid oklch(50% 10% 200/40%);
    padding-bottom: calc(10px - 0.4em);
  }


  nav a:hover {
  border-bottom:0.4em solid var(--color-accent) ;
  background-color: color-mix(in oklch, var(--color-accent), canvas 85%);
  }

</style>



YOLO <slot/>
