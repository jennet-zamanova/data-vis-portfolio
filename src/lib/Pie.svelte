<script>
    import * as d3 from 'd3';
    let arcGenerator = d3.arc().innerRadius(0).outerRadius(50);
    let arc = arcGenerator({
        startAngle: 0,
        endAngle: 2 * Math.PI
    });

    export let data = [];



    let colors = d3.scaleOrdinal(d3.schemeTableau10);

    let sliceGenerator = d3.pie().value(d => d.value);

    let arcData;
    let arcs;
    $: {
        arcData = sliceGenerator(data);
        arcs = arcData.map(d => arcGenerator(d));
    }  

    export let selectedIndex = -1;

</script>


<div class="container">
	<svg viewBox="-50 -50 100 100">
        {#each arcs as arc, index}
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <!-- svelte-ignore a11y-no-static-element-interactions -->
        <path d={ arc } fill={ colors(index) } 
            class:selected={selectedIndex === index}
            on:click={e => selectedIndex = selectedIndex === index ? -1 : index}
        />
        {/each}
    </svg>
	<ul class="legend">
        {#each data as d, index}
            <li style="--color: { colors(index) }" class:selected={selectedIndex === index}>
                <span class="swatch"></span>
                {d.label} <em>({d.value})</em>
            </li>
        {/each}
    </ul>
</div>


<style>
    svg {
        max-width: 20em;
        margin-block: 2em;

        /* Do not clip shapes outside the viewBox */
        overflow: visible;
    }
    svg:has(path:hover) path:not(:hover), svg:has(.selected) path:not(.selected){
        opacity: 50%;
    }

    path {
        transition: 300ms;
        cursor: pointer;
    }

    path:hover {
        opacity: 100% !important;
    }


    .selected {
        --color: oklch(60% 45% 0) !important;
        
        &:is(path) {
            fill: var(--color) !important;
        }
        
        &:is(li) {
            color: var(--color);
        }
    }

    .swatch {
        display: inline-block;
        background-color: var(--color);
        width: 20px;
        height: 20px;
    }
    ul {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(8em, 1fr));
    }

    li {
        display: flex;
        align-items: center;
        gap: 0.25em;
    }

    ul:has(.selected) li:not(.selected) {
        color: gray;
    }

    .legend {
       border: 1px solid black;
       padding: 1em;
       margin: 1em;
       flex: 1;
    }

    .container {
        display: flex;
        align-items: center;
        gap: 2em;
    }

</style>
