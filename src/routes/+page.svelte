<script>
	import Test from '$lib/Test.svelte';
	export let data;

	$: ({ latest_episode, all_episodes } = data);
</script>

<h1>Welcome to SvelteKit</h1>
<h2>{latest_episode.title}</h2>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
<br />
<nav>
	{#each all_episodes as episode}
		<a href="/episodes/{episode.slug}">{episode.title}</a><br />
	{/each}<br />
</nav>
<a href="/about">About Us</a>

<Test />
