<script>
    export let data = {};
    export let hLevel = 2;
</script>

<article>
    <svelte:element this={"h" + hLevel}><a href={data.href}>{data.title}</a></svelte:element>
    <img src={data.image} alt="">
    <div>
        {@html data.description}
        <p>C. {data.year}</p>
    </div>
</article>


<style>

article {
  grid-template-rows: subgrid;
  grid-row: span 3;
}

article > h2 {
  margin: 0;
}

article > h3 {
  margin: 0;
}

p {
  font-style: italic;
}
</style>