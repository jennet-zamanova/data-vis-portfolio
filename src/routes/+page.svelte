<script>
    import projects from "$lib/projects.json";
    import Project from "$lib/Project.svelte";
    import { onMount } from "svelte";

    let githubData = null;
    let loading = true;
    let error = null;

    onMount(async () => {
      try {
        const response = await fetch("https://api.github.com/users/jennet-zamanova");
        githubData = await response.json();
      } catch (err) {
        error = err;
      }
      loading = false;
    });
</script>

<svelte:head>
  <title>Home</title>
</svelte:head>

<h1>Jennet</h1>

<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Molestias illo quibusdam vero pariatur harum rerum distinctio optio saepe, suscipit, ab voluptatum eligendi nesciunt, possimus cupiditate quis facilis deserunt impedit enim.</p>
<img src="images/Jennet.jpg" alt="Jennet in front the main dome at MIT" width="550px">

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