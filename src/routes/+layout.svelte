<script>
    import { page } from "$app/stores";

    let localStorage = globalThis.localStorage ?? {};


    let pages = [
	{url: "./", title: "Homes"},
	{url: "projects", title: "Projects"},
    {url: "contact", title: "Contact"},
	{url: "resume", title: "Resume"},
    {url: "https://github.com/jennet-zamanova", title: "Github"}
];
    let colorScheme = localStorage.colorScheme ?? "light dark";

    let root = globalThis?.document?.documentElement;
    $: root?.style.setProperty("color-scheme", colorScheme);

    $: localStorage.colorScheme = colorScheme;
    
</script>

<nav>
    {#each pages as p}
    <a href={p.url} class:current={$page.route.id === "/"+p.url || "." + $page.route.id === p.url} target={p.url.startsWith("http") ? "_blank" : null}>
        {p.title}
    </a>

    {/each}
</nav>

<label class="color-scheme">
    Theme:
    <select bind:value={ colorScheme }>
        <option value="light dark">Automatic</option>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
    </select>
</label>


<slot />

<style>

a {
  flex: 1;
  text-decoration: none;
  color: inherit;
  text-align: center;
  padding: 0.5em;
}


.current {
  border-bottom-width: 0.4em;
  border-bottom-style: solid;
  border-bottom-color: var(--border-color);
}

a:hover {
  border-bottom-width: 0.4em;
  border-bottom-style: solid;
  border-bottom-color: var(--color-accent);
  background-color: color-mix(in oklch, var(--color-accent), canvas 85%);
}

.color-scheme{
  position: absolute;
  top: 2rem;
  right: 2rem;
  display: inline-flex;
  gap: 4px;
  font-size: 80%;
}
</style>