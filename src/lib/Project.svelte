<script>
    export let data = {};
    export let hLevel=2;
</script>

<article>
    <svelte:element this={"h" + hLevel}>{data.title}</svelte:element>
    <p><strong>{data.year} </strong></p>
    <img src={data.image} alt="">
    <p> {data.description}</p>
</article>

<style>
article {
  display: grid;
  grid-template-rows: subgrid;
  grid-row: span 4;
  gap: 0.5rem;
}

article h2 {
  margin: 0;
  margin-bottom: 0.5rem;
}

article p {
  margin-bottom: 1rem;
}

img{
  max-width: 100%;
}

</style>
