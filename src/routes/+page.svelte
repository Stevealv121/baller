<script>
	let players = [
		{
			name: 'Cristiano',
			goals: 10
		},
		{
			name: 'Messi',
			goals: 10
		}
	];

	let name = '';
	let error = '';

	const addPlayer = () => {
		let new_player = {
			name: name,
			goals: 0
		};

		if (name !== '') {
			players = [...players, new_player];
			error = '';
		} else {
			error = 'Name cannot be empty';
		}
		name = '';
	};

	const addGoal = (index) => {
		players[index].goals += 1;
	};
	const removeGoal = (index) => {
		players[index].goals -= 1;
	};
	const deletePlayer = (index) => {
		let deleteItem = players[index];
		players = players.filter((item) => item !== deleteItem);
	};
</script>

<input type="text" placeholder="Add new player" bind:value={name} />
<button on:click={addPlayer}>Add</button>
<br />

<ul>
	{#each players as player, index}
		<li>
			<span>{player.name} - </span>
			<span>{player.goals} goals</span>
			<span><button on:click={() => addGoal(index)}>+</button></span>
			<span><button on:click={() => removeGoal(index)}>-</button></span>
			<span><button on:click={() => deletePlayer(index)}>Delete</button></span>
		</li>
	{:else}
		<p>There are no players</p>
	{/each}
	<p class="error">{error}</p>
</ul>

<a href="/about">About Us</a>

<style>
	.error {
		color: red;
	}
</style>
