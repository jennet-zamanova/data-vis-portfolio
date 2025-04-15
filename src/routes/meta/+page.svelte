<svelte:head>
  <title>Meta</title>
</svelte:head>

<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";
  import Bar from '$lib/StackedBar.svelte';
  import FileLines from '$lib/FileLines.svelte';
  import { base } from '$app/paths';

  import CommitScatterplot from "./Scatterplot.svelte"
  import Scrolly from "svelte-scrolly";


  let data = [];
  let commits = [];
  let clickedCommits = [];

  let commitProgress = 100;
  let raceProgress = 100;
  let width = 1000;

  let colorScale = d3.scaleOrdinal(d3.schemeTableau10);

  onMount(async () => {
    data = await d3.csv(`${base}/loc.csv`, row => ({
      ...row,
      line: Number(row.line), // or just +row.line
      depth: Number(row.depth),
      length: Number(row.length),
      date: new Date(row.date + "T00:00" + row.timezone),
      datetime: new Date(row.datetime)
    }));
    commits = d3.groups(data, d => d.commit).map(([commit, lines]) => {
      let first = lines[0];
      let {author, date, time, timezone, datetime} = first;
      let ret = {
        id: commit,
        url: "https://github.com/vis-society/lab-7/commit/" + commit,
        author, date, time, timezone, datetime,
        hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
        totalLines: lines.length
      };

      // Like ret.lines = lines
      // but non-enumerable so it doesn’t show up in JSON.stringify
      Object.defineProperty(ret, "lines", {
        value: lines,
        configurable: true,
        writable: true,
        enumerable: false,
      });

      return ret;
    });
    commits = commits.filter(commit => !/^0+$/.test(commit.id));
  });


  $: timeScale = d3.scaleTime()
    .domain(d3.extent(commits.map(d => d.datetime)))
    .range([0, 100]);
  $: commitMaxTime = timeScale.invert(commitProgress);

  $: raceMaxTime = timeScale.invert(raceProgress);

  $: filteredCommits = commits.filter(commit => commit.datetime <= commitMaxTime)

  $: filteredLines = data.filter(d => d.datetime <= commitMaxTime)

  $: raceLines = data.filter(d => d.datetime <= raceMaxTime)

  $: allTypes = Array.from(new Set(filteredLines.map(d => d.type)));

  $: selectedLines = (clickedCommits.length > 0 ? clickedCommits : filteredCommits).flatMap(d => d.lines);

  $: selectedCounts = d3.rollup(
    selectedLines,
    v => v.length,
    d => d.type
  );

  $: languageBreakdown = allTypes.map(type => [type, selectedCounts.get(type) || 0]);

  function updateClickedCommits(commits) {
    clickedCommits = commits;
  }
</script>


<h1>Meta</h1>

<p>Total lines of code: {filteredLines.length}</p>
<dl class="stats">
  <dt>Commits</dt>
	<dd>{filteredCommits.length}</dd>
	<dt>Total <abbr title="Lines of code">LOC</abbr></dt>
	<dd>{filteredLines.length}</dd>
  <dt>Files</dt>
	<dd>{(new Set(filteredLines.map((d) => d["file"]))).size}</dd>
  <dt>Maximum file length</dt>
	<dd>{d3.max(filteredLines.map((d) => d["line"]))}</dd>
  <dt>Maximum depth</dt>
	<dd>{d3.max(filteredLines.map((d) => d["depth"]))}</dd>
  
</dl>

<Scrolly bind:progress={ commitProgress }>
	{#each commits as commit, index }
    <p>
      On {commit.datetime.toLocaleString("en", {dateStyle: "full", timeStyle: "short"})},
      {index === 0 
        ? "I set forth on my very first commit, beginning a magical journey of code. You can view it "
        : "I added another enchanted commit, each line sparkling with a touch of wonder. See it "}
      <a href="{commit.url}" target="_blank">
        {index === 0 ? "here" : "here"}
      </a>.
      This update transformed {commit.totalLines} lines across { d3.rollups(commit.lines, D => D.length, d => d.file).length } files.
      With every commit, our project grows into a kingdom of dreams.
    </p>
  {/each}

	<svelte:fragment slot="viz">
    <CommitScatterplot commits={filteredCommits} updateClickedCommits={updateClickedCommits}/>
    <Bar data={languageBreakdown} width={0.4*width} colorScale={colorScale}/>
	</svelte:fragment>
</Scrolly>


<Scrolly bind:progress={raceProgress} --scrolly-layout="viz-first">
  {#each commits as commit, index }
    <p>
      On {commit.datetime.toLocaleString("en", {dateStyle: "full", timeStyle: "short"})},
      {index === 0 
        ? "I set forth on my very first commit, beginning a magical journey of code. You can view it "
        : "I added another enchanted commit, each line sparkling with a touch of wonder. See it "}
      <a href="{commit.url}" target="_blank">
        {index === 0 ? "here" : "here"}
      </a>.
      This update transformed {commit.totalLines} lines across { d3.rollups(commit.lines, D => D.length, d => d.file).length } files.
      With every commit, our project grows into a kingdom of dreams.
    </p>
  {/each}
  <svelte:fragment slot="viz">
    <FileLines lines={raceLines} width={width} colorScale={colorScale}/>
	</svelte:fragment>
</Scrolly>






<style>

:global(body) {
  max-width: min(120ch, 80vw);
}

dl {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
}


.stats dt {
  grid-row: 1; /* Place all <dt> elements in the first row */
  font-weight:lighter;
  font-size: medium;
}

.stats dd {
  grid-row: 2; /* Place all <dd> elements in the second row */
  margin: 0px;
  font-weight: bold;
  font-size: larger;
}

label {
  display: flex;
}

label input {
  flex: 1;
}

.slider {
  width: 100%;
  text-align: right;
  white-space: nowrap
}
</style>