<script>
	import ToDoList from './lib/ToDoList.svelte';
	import { v4 as uuid } from 'uuid';
	import { onMount, tick } from 'svelte';

	let toDoListBox;
	let showList = true;

	let toDoLists = null;
	let error = null;
	let isLoading = false;
	let isAdding = false;

	onMount(() => {
		LOAD_toDoLists();
	});

	async function LOAD_toDoLists() {
		isLoading = true;

		await fetch('https://jsonplaceholder.typicode.com/todos?_limit=10').then(async (response) => {
			if (response.ok) {
				toDoLists = await response.json();
			} else {
				error = 'An error has occured.';
			}
		});

		isLoading = false;
	}

	async function handleAddToDoLists(event) {
		event.preventDefault();
		isAdding = true;

		await fetch('https://jsonplaceholder.typicode.com/todos', {
			method: 'POST',
			body: JSON.stringify({
				title: event.detail.title,
				completed: false
			}),
			headers: {
				'Content-type': 'application/json;charset=UTF-8'
			}
		}).then(async (response) => {
			if (response.ok) {
				const toDoList = await response.json();
				toDoLists = [...toDoLists, { ...toDoList, id: uuid() }];
				toDoListBox.clearInput();
			} else {
				alert('An error has occured.');
			}
		});

		isAdding = false;
		await tick();
		toDoListBox.focusInput();
	}

	function handleRemoveToDoLists(event) {
		toDoLists = toDoLists.filter((toDoItem) => toDoItem.id !== event.detail.id);
	}

	function handleToggleToDoLists(event) {
		toDoLists = toDoLists.map((toDoItem) => {
			if (toDoItem.id === event.detail.id) {
				return { ...toDoItem, completed: event.detail.value };
			}
			return { ...toDoItem };
		});
	}
</script>

<label>
	<input type="checkbox" bind:checked={showList} />
	Show/Hide Tasks
</label>

{#if showList}
	<div style:max-width="20rem">
		<ToDoList
			{toDoLists}
			{error}
			{isLoading}
			disableAdding={isAdding}
			bind:this={toDoListBox}
			on:addtodo={handleAddToDoLists}
			on:removetodo={handleRemoveToDoLists}
			on:toggletodo={handleToggleToDoLists}
		/>
	</div>
{/if}

<style>
</style>
