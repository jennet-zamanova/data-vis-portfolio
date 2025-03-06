<script>
    import projects from "$lib/projects.json";
    import Project from "$lib/Project.svelte";
</script>

<svelte:head>
  <title>Home</title>
</svelte:head>

<h1>Jennet</h1>

<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Molestias illo quibusdam vero pariatur harum rerum distinctio optio saepe, suscipit, ab voluptatum eligendi nesciunt, possimus cupiditate quis facilis deserunt impedit enim.</p>
<img src="images/Jennet.jpg" alt="Jennet in front the main dome at MIT" width="550px">

{#await fetch("https://api.github.com/users/jennet-zamanova")}
  <p>Loading...</p>
{:then response}
  {#await response.json()}
    <p>Decoding...</p>
  {:then data}
    <section>
      <h2>My GitHub Stats</h2>
      <dl>
        <dt>Followers</dt>
        <dd>{data.followers}</dd>
        <dt>Following</dt>
        <dd>{data.following}</dd>
        <dt>Public Repositories</dt>
        <dd>{data.public_repos}</dd>
      </dl>
    </section>
  {:catch error}
    <p class="error">Something went wrong: {error.message}</p>
  {/await}
{:catch error}
  <p class="error">Something went wrong: {error.message}</p>
{/await}

<h2>Recent Projects</h2>
<div class="projects">
    {#each projects.slice(0, 3) as p}
    <Project data={p} hLevel="3"/>
    {/each}

</div>

<style>
  dl {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
}

dt {
  grid-row: 1; /* Place all <dt> elements in the first row */
    font-weight:lighter;
    font-size: medium;
}

dd {
  grid-row: 2; /* Place all <dd> elements in the second row */
    margin: 0px;
    font-weight: bold;
    font-size: larger;
}
</style>