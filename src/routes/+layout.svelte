
<script>
import { page } from "$app/stores";
import "../styles/style.css";


let pages = [
	{url: "/", title: "Home"},
	{url: "projects", title: "Projects"},
    {url: "contact", title: "Contact"},
    {url: "CV", title: "Resume"},
    {url: "https://github.com/marinegapihan2", title: "GitHub" }
];

let colorScheme = globalThis.localStorage?.getItem("colorScheme") ?? "light";

let root = globalThis?.document?.documentElement;

$: root?.style.setProperty("color-scheme", colorScheme);

$: {
    if (globalThis.localStorage) {
        globalThis.localStorage.setItem("colorScheme", colorScheme);
    }
}

</script>

<!-- <p>Current theme: {colorScheme}</p> -->

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

<label class="color-scheme">
    Theme:
    <select bind:value={ colorScheme }>
        <option value="dark light">Automatic</option>
        <option value="dark">Dark</option>
        <option value="light">Light</option>
    </select>
</label>

<style>

</style>

<slot/>
