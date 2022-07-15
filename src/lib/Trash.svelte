<script>
	// @ts-nocheck

	let plastik;
	let restmuell;
	let bioabfall;
	let altpapier;
	let category;
	let trash_pos_x;
	let trash_pos_y;
	let elementName = '';
	let dropped_in = true;
	let activeEvent = '';
	let originalX = '';
	let originalY = '';
	let status = '';

	async function getTrash() {
		let response = await fetch('./../Trash.json');
		let trashToSort = await response.json();
		return trashToSort;
	}
	const promise = getTrash();

	function handleDragEnter(e) {
		status = 'Du hebst ' + elementName + ' über den ' + e.target.getAttribute('id');
		let pageX = parseInt(e.target.style.left) - 50;
		let pageY = parseInt(e.target.style.top) - 50;
		var element_id = e.dataTransfer.getData('text');
		if (
			detectTouchEnd(
				element_id.offsetLeft,
				element_id.offsetTop,
				pageX,
				pageY,
				element_id.offsetWidth,
				element_id.offsetHeight
			)
		) {
			dropped_in = true;
		} else {
			dropped_in = false;
		}
	}

	function handleDragLeave(e) {
		status = 'Du hast ' + elementName + ' losgelassen';
	}

	function handleDragDrop(e) {
		console.log('Drop it like its hot');
		var element_title = e.dataTransfer.getData('text');
		var element_id = e.dataTransfer.getData('id');
		let currentTrashElement = document.getElementById(element_id);
		if (category == e.target.getAttribute('id')) {
			status = 'Du hast ' + element_title + ' in den ' + e.target.getAttribute('id') + ' getan.';
			console.log(currentTrashElement);
			trash_pos_x = e.target.offsetLeft + 50;
			trash_pos_y = e.target.offsetTop + 50;
			currentTrashElement.setAttribute(
				'style',
				'position: absolute; max-width: 50px; top: ' +
					trash_pos_y +
					'px; left: ' +
					trash_pos_x +
					'px;'
			);
			status = 'Richtig. ' + currentTrashElement.getAttribute('name');
		} else {
			status = 'Falsch. ' + currentTrashElement.getAttribute('name');
		}
	}

	function handleDragStart(e) {
		elementName = e.target.getAttribute('title');
		status = 'Du hebst ' + elementName + ' auf';
		e.dataTransfer.dropEffect = 'move';
		e.dataTransfer.setData('text', e.target.getAttribute('title'));
		e.dataTransfer.setData('id', e.target.getAttribute('id'));
		e.dataTransfer.setData('desc', e.target.getAttribute('desc'));
		category = e.target.getAttribute('value');
		console.log(category);
	}
	function handleDragEnd(e) {
		if (dropped_in == false) {
			status = 'Du hast ' + e.target.getAttribute('title') + ' fallengelassen.';
		}
		dropped_in = false;
	}

	function handleTouchStart(e) {
		status = 'Touch start with element ' + e.target.getAttribute('id');
		originalX = e.target.offsetLeft - 10 + 'px';
		originalY = e.target.offsetTop - 10 + 'px';
		activeEvent = 'start';
	}

	function handleTouchMove(e) {
		let touchLocation = e.targetTouches[0];
		let pageX = Math.floor(touchLocation.pageX - 50) + 'px';
		let pageY = Math.floor(touchLocation.pageY - 50) + 'px';
		status = 'Touch x ' + pageX + ' Touch y ' + pageY;
		e.target.style.position = 'absolute';
		e.target.style.left = pageX;
		e.target.style.top = pageY;
		activeEvent = 'move';
	}

	function handleTouchEnd(e) {
		e.preventDefault();
		if (activeEvent === 'move') {
			let pageX = parseInt(e.target.style.left) - 50;
			let pageY = parseInt(e.target.style.top) - 50;
			var element_id = e.dataTransfer.getData('text');

			if (
				detectTouchEnd(
					element_id.offsetLeft,
					element_id.offsetTop,
					pageX,
					pageY,
					element_id.offsetWidth,
					element_id.offsetHeight
				)
			) {
				e.target.style.position = 'initial';
				dropped_in = true;
				status = 'You dropped ' + e.target.getAttribute('id') + ' into drop zone';
			} else {
				e.target.style.left = originalX;
				e.target.style.top = originalY;
				status = 'You let the ' + e.target.getAttribute('id') + ' go.';
			}
		}
	}

	function detectTouchEnd(x1, y1, x2, y2, w, h) {
		if (x2 - x1 > w) return false;
		if (y2 - y1 > h) return false;
		return true;
	}
</script>

<h3 id="app_status">{status}</h3>
<div
	on:dragenter={handleDragEnter}
	on:dragleave={handleDragLeave}
	on:drop={handleDragDrop}
	bind:this={plastik}
	class="muellcontainer"
	id="plastikmuell"
	ondragover="return false"
>
	<h4>Plastik</h4>
</div>
<div
	on:dragenter={handleDragEnter}
	on:dragleave={handleDragLeave}
	on:drop={handleDragDrop}
	bind:this={restmuell}
	class="muellcontainer"
	id="restmuell"
	ondragover="return false"
>
	<h4>Restmüll</h4>
</div>
<div
	on:dragenter={handleDragEnter}
	on:dragleave={handleDragLeave}
	on:drop={handleDragDrop}
	bind:this={bioabfall}
	class="muellcontainer"
	id="bioabfall"
	ondragover="return false"
>
	<h4>Biomüll</h4>
</div>
<div
	on:dragenter={handleDragEnter}
	on:dragleave={handleDragLeave}
	on:drop={handleDragDrop}

	class="muellcontainer"
	id="papiermuell"
	ondragover="return false"
>
	<h4>Altpapier</h4>
</div>

<div class="trash">
	{#await promise}
		<p>Loading...</p>
	{:then trash}
		{#each trash as trash}
			<div
				id={trash.id}
				name={trash.desc}
				value={trash.title}
				draggable="true"
				bind:this={trash.element}
				on:dragstart={handleDragStart}
				on:dragend={handleDragEnd}
				on:touchstart={handleTouchStart}
				on:touchmove={handleTouchMove}
				on:touchend={handleTouchEnd}
			>
				<img
					class="trash-item-img"
					id={trash.id}
					value={trash.category}
					src={trash.img}
					alt={trash.title}
					title={trash.title}
				/>
			</div>
		{/each}
	{:catch error}
		<p style="color: red">{error.message}</p>
	{/await}
</div>

<style>
	.muellcontainer {
		border: #999 1px solid;
		width: 300px;
		height: 200px;
		padding: 8px;
		font-size: 19px;
		margin: 10px;
		display: inline-block;
	}
	.trash {
		display: inline-flex;
	}
	.trash-item-img {
		max-width: 150px;
	}
	#plastikmuell {
		background-color: yellow;
	}
	#restmuell {
		background-color: grey;
	}
	#bioabfall {
		background-color: rgb(89, 40, 9);
	}
	#papiermuell {
		background-color: skyblue;
	}
	h4 {
		font-size: 1.2em;
		color: white;
		text-shadow: 0px 0px 4px black;
		padding-left: 25px;
	}
</style>
