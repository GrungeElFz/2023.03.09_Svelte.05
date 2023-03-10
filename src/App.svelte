<script>
	import ToDoList from './lib/ToDoList.svelte';
	import { v4 as uuid } from 'uuid';

	let showList = true;
	let toDoListBox;
	let toDoLists = null;

	function LOAD_toDoLists() {
		return fetch('https://jsonplaceholder.typicode.com/todos').then((response) => {
			if (response.ok) {
				return response.json();
			} else {
				throw new Error('An error has occurred.');
			}
		});
	}

	function handleAddToDoLists(event) {
		event.preventDefault();

		toDoLists = [
			...toDoLists,
			{
				id: uuid(),
				title: event.detail.title,
				completed: false
			}
		];

		toDoListBox.clearInput();
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
	{#await LOAD_toDoLists() then toDoLists }
		<div style:max-width="20rem">
			<ToDoList
				{toDoLists}
				bind:this={toDoListBox}
				on:addtodo={handleAddToDoLists}
				on:removetodo={handleRemoveToDoLists}
				on:toggletodo={handleToggleToDoLists}
			/>
		</div>
	{/await}
{/if}

<style>
</style>
