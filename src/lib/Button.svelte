<script>
	let counter = 0;
	$: doubled = counter * 2;
	function handleClick() {
		counter++;
	}
</script>

<p>Du hast {counter}-mal geklickt</p>
<button on:click={handleClick}>Klick hier</button>
<p>Der doppelte Wert berägt: {doubled}</p>
