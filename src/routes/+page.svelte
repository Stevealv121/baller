<script>
	import { initializeApp } from 'firebase/app';
	import {
		getFirestore,
		collection,
		doc,
		updateDoc,
		increment,
		onSnapshot,
		deleteDoc,
		addDoc
	} from 'firebase/firestore';
	import { firebaseConfig } from '$lib/firebaseConfig.js';
	import { IconTrashFilled } from '@tabler/icons-svelte';
	import { IconPlus, IconMinus } from '@tabler/icons-svelte';

	const firebaseApp = initializeApp(firebaseConfig);
	const db = getFirestore(firebaseApp);
	//console.log({ firebaseApp, db });

	const colRef = collection(db, 'players');

	let players = [];

	const unsubscribe = onSnapshot(colRef, (querySnapshot) => {
		let fbPlayers = [];
		querySnapshot.forEach((doc) => {
			let player = { ...doc.data(), id: doc.id };
			fbPlayers = [player, ...fbPlayers];
		});
		//order players by goals
		fbPlayers.sort((a, b) => b.goals - a.goals);
		players = fbPlayers;
	});

	let name = '';
	let error = '';

	const addPlayer = async () => {
		let new_player = {
			name: name,
			goals: 0
		};

		const docRef = await addDoc(collection(db, 'players'), {
			name: new_player.name,
			goals: new_player.goals
		});

		if (name !== '') {
			error = '';
		} else {
			error = 'Name cannot be empty';
		}
		name = '';
	};

	const addGoal = async (item) => {
		const playerRef = doc(db, 'players', item.id);

		await updateDoc(playerRef, {
			goals: increment(1)
		});
	};
	const removeGoal = async (item) => {
		const playerRef = doc(db, 'players', item.id);

		await updateDoc(playerRef, {
			goals: increment(-1)
		});
	};
	const deletePlayer = async (item) => {
		await deleteDoc(doc(db, 'players', item.id));
	};
</script>

<section class="grid gap-8 px-10 md:items-center md:text-left">
	<br />
	<h2 class="mb-2 text-4xl font-medium">Top Scorers</h2>
	<input type="text" placeholder="Add new player" bind:value={name} />
	<button type="button" class="variant-filled-primary btn" on:click={addPlayer}>Add</button>
	<br />

	<ol class="list">
		{#each players as player}
			<div class="card p-4">
				<div class="grid gap-8 sm:grid-cols-5 md:items-center">
					<div class="card variant-filled-primary p-4 sm:text-center">
						{player.name}
					</div>
					<div class="card variant-filled-primary p-4 sm:text-center">{player.goals} goals</div>
					<span
						><button
							type="button"
							class="variant-filled-primary btn"
							on:click={() => addGoal(player)}><IconPlus></IconPlus></button
						></span
					>
					<span
						><button
							type="button"
							class="variant-filled-primary btn"
							on:click={() => removeGoal(player)}><IconMinus></IconMinus></button
						></span
					>
					<span
						><button
							type="button"
							class="variant-filled-primary btn"
							on:click={() => deletePlayer(player)}><IconTrashFilled /></button
						></span
					>
				</div>
			</div>
		{:else}
			<p>There are no players</p>
		{/each}
		<p class="error">{error}</p>
	</ol>
	<br />

	<a href="/about">About Us</a>
</section>

<style>
	.error {
		color: red;
	}
</style>
