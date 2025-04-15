<svelte:head>
  <title>Meta</title>
</svelte:head>

<script>
  import * as d3 from "d3";
  import {
    computePosition,
    autoPlacement,
    offset,
  } from '@floating-ui/dom';

  export let commits = [];
  export let updateClickedCommits;


  let width = 1000, height = 600;
  let margin = {top: 10, right: 10, bottom: 30, left: 20};

  let usableArea = {
    top: margin.top,
    right: width - margin.right,
    bottom: height - margin.bottom,
    left: margin.left
  };
  usableArea.width = usableArea.right - usableArea.left;
  usableArea.height = usableArea.bottom - usableArea.top;

  let xAxis, yAxis, yAxisGridlines;
  let commitTooltip;


  // let cursor = {x: 0, y: 0};
  let tooltipPosition = {x: 0, y: 0};
  let hoveredIndex = -1;
  let clickedCommits = [];


  async function dotInteraction (index, evt) {
    let hoveredDot = evt.target;
    if (evt.type === "mouseenter") {
      hoveredIndex = index;
      // cursor = {x: evt.x, y: evt.y};
      tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
        strategy: "fixed", // because we use position: fixed
        middleware: [
          offset(5), // spacing from tooltip to dot
          autoPlacement() // see https://floating-ui.com/docs/autoplacement
        ],
      });        }
    else if (evt.type === "mouseleave") {
      hoveredIndex = -1
    }
    else if (evt.type === "click") {
      let commit = commits[index]
      if (!clickedCommits.includes(commit)) {
        // Add the commit to the clickedCommits array
        clickedCommits = [...clickedCommits, commit];
      }
      else {
          // Remove the commit from the array
          clickedCommits = clickedCommits.filter(c => c !== commit);
      }
      updateClickedCommits(clickedCommits);
    }

  }



  $: xScale = d3.scaleTime()
		.domain(d3.extent(commits.map(d => d.datetime)))
		.range([usableArea.left, usableArea.right])
    .nice();
	
  $: yScale = d3.scaleLinear()
        .domain([24, 0])
        .range([usableArea.bottom, usableArea.top]);

  $: {
    d3.select(xAxis).call(d3.axisBottom(xScale));
    d3.select(yAxis).call(d3.axisLeft(yScale).tickFormat(d => String(d % 24).padStart(2, "0") + ":00"));
  }

  $: {
    d3.select(yAxisGridlines).call(
      d3.axisLeft(yScale)
        .tickFormat("")
        .tickSize(-usableArea.width)
    );
  }

  $: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};
</script>



<svg viewBox="0 0 {width} {height}">
  <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
  <g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridlines} />
  <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
  <g class="dots">
    {#each commits as commit, index (commit.id)}
      <circle
        cx={ xScale(commit.datetime) }
        cy={ yScale(commit.hourFrac) }
        r="5"
        fill="steelblue"
        on:mouseenter={evt => dotInteraction(index, evt)}
	      on:mouseleave={evt => dotInteraction(index, evt)}
        on:click={ evt => dotInteraction(index, evt) }
        class:selected={ clickedCommits.includes(commit) }
      />
    {/each}
  </g>
</svg>
<!-- Other attributes omitted for brevity -->

<dl class="info tooltip" hidden={hoveredIndex === -1} bind:this={commitTooltip}  style="top: {tooltipPosition.y}px; left: {tooltipPosition.x}px">
    <dt>Commit</dt>
    <dd><a href="{ hoveredCommit.url }" target="_blank">{ hoveredCommit.id }</a></dd>
  
    <dt>Date</dt>
    <dd>{ hoveredCommit.datetime?.toLocaleString("en", {dateStyle: "full"}) }</dd>
  
    <dt>Time</dt>
    <dd>{ hoveredCommit.time?.toLocaleString("en", {dateStyle: "full"}) }</dd>
  
    <dt>Author</dt>
    <dd>{ hoveredCommit.author?.toLocaleString("en", {dateStyle: "full"}) }</dd>
  
    <dt>Lines Added</dt>
    <dd>{ hoveredCommit.totalLines?.toLocaleString("en", {dateStyle: "full"}) }</dd>
  
    <!-- Add: Time, author, lines edited -->
  </dl>



<style>
.selected {
    fill: var(--color-accent);
}

dl.info {
  display: grid;
  grid-template-columns: auto 1fr;
  gap: 0.5rem 1rem;
  align-items: baseline;
  transition-duration: 500ms;
	transition-property: opacity, visibility;

	&[hidden]:not(:hover, :focus-within) {
		opacity: 0;
		visibility: hidden;
	}
}

dl.info dt {
  font-weight: lighter;
  margin: 0;
  text-align: right; 
}

dl.info dd {
  font-weight: bold;
  margin: 0;
}

.tooltip {
  position: fixed;
  top: 20em;
  left: 1em;
  background-color: oklch(100% 0% 0 / 80%);
  /* oklch(100% 0% 0 / 80%); */
  border-radius: 0.5em;
  box-shadow: 6px 6px 2px 2px rgba(98, 98, 105, 0.2);
  padding: 1em;
  backdrop-filter: blur(10px);
}

svg {
		overflow: visible;
}

.gridlines {
	stroke-opacity: .2;
}

circle {
	transition: 200ms;
  transform-origin: center;
  transform-box: fill-box;


	&:hover {
		transform: scale(1.5);
	}

  @starting-style {
    r: 0;
  }

}
</style>