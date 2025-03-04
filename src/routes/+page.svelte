<script>
    import projects from "$lib/projects.json";
    import Project from "$lib/Project.svelte";
</script>


<h1>Jennet</h1>
<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Molestias illo quibusdam vero pariatur harum rerum distinctio optio saepe, suscipit, ab voluptatum eligendi nesciunt, possimus cupiditate quis facilis deserunt impedit enim.</p>
<img src="images/Jennet.jpg" alt="Jennet in front the main dome at MIT" width="550px">


<h2>Recent Projects</h2>
<div class="projects">
    {#each projects.slice(0, 3) as p}
    <Project data={p} hLevel="3"/>
    {/each}

</div>