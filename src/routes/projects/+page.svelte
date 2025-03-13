<svelte:head>
  <title>Projects</title>
</svelte:head>

<script>
    import projects from "$lib/projects.json";
    import Project from "$lib/Project.svelte";
    import Pie from '$lib/Pie.svelte';
    import {rollups} from "d3";

    let query = "";
    let pieData;
    let filteredProjects;

    $: {
      filteredProjects = projects.filter(project => {
        let values = Object.values(project).join("\n").toLowerCase();
        return values.includes(query.toLowerCase());
      });

      let rolledData = rollups(filteredProjects, v => v.length, d => d.year);

      pieData = rolledData.map(([year, count]) => {
        return { value: count, label: year };
      });
    }

    let selectedYearIndex = -1;
    let selectedYear;
    $: selectedYear = selectedYearIndex > -1 ? pieData[selectedYearIndex].label : null;

    $: filteredByYear = filteredProjects.filter(project => {
        if (selectedYear) {
            return project.year === selectedYear;
        }

        return true;
    });


</script>
  

<input type="search" bind:value={query}
       aria-label="Search projects" placeholder="🔍 Search projects…" />

<h1>{ filteredByYear.length } Projects in {selectedYear || "all years"}</h1>

<Pie data={pieData} bind:selectedIndex={selectedYearIndex}/>
<div class="projects">
    {#each filteredByYear as p}
    <Project data={p} />
    {/each}
</div>

<style>
  input {
    width: 100%;
  }

</style> 