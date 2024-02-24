<script>
	import '../app.pcss';
	import Header from './Header.svelte';
	import Footer from './Footer.svelte';

	//import './styles.css';
	import { AppBar } from '@skeletonlabs/skeleton';
	import { IconMenu2 } from '@tabler/icons-svelte';
	import { IconUserHexagon } from '@tabler/icons-svelte';
	import { LightSwitch } from '@skeletonlabs/skeleton';
</script>

<!-- 
<Header></Header> -->
<AppBar gridColumns="grid-cols-3" slotDefault="place-self-center" slotTrail="place-content-end">
	<svelte:fragment slot="lead"><IconMenu2></IconMenu2></svelte:fragment>
	BALLER
	<svelte:fragment slot="trail"><LightSwitch /></svelte:fragment>
</AppBar>
<main>
	<slot />
</main>

<Footer></Footer>
