
<script>
	import Draggable from '$lib/Draggable.svelte';
	import Trash from '$lib/Trash.svelte';
	import Footer from '$lib/Footer.svelte';
	import Header from '$lib/Header.svelte';
</script>

<Header />

<Trash />

<Draggable>Mich kann man bewegen</Draggable>

<Footer />

<style>
	:global(*) {
		font-family: Arial, Helvetica, sans-serif;
	}
</style>

