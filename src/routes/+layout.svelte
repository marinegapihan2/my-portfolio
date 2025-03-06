
<script>
let pages = [
	{url: "./", title: "Home"},
	{url: "projects", title: "Projects"},
    {url: "contact", title: "Contact"},
    {url: "CV", title: "Resume"},
    {url: "https://github.com/marinegapihan2", title: "GitHub" }
];

import { page } from "$app/stores";


</script>

<nav>
    {#each pages as p}
    <a href="{p.url}">{p.title}</a>
    {/each}
</nav>

YOLO <slot/>
