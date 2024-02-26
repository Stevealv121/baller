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
	import { SlideToggle } from '@skeletonlabs/skeleton';
	import { IconUserCircle } from '@tabler/icons-svelte';

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
	let edit = false;
</script>

<section class="grid max-w-3xl gap-8 px-10 md:items-center md:text-left">
	<br />
	<h2 class="mb-2 text-4xl font-medium">Top Scorers</h2>
	<input type="text" placeholder="Add new player" bind:value={name} />
	<button type="button" class="variant-filled-primary btn" on:click={addPlayer}>Add</button>
	<br />
	<SlideToggle name="slider-labeled" bind:checked={edit}>Edit</SlideToggle>

	<ol class="list">
		{#each players as player, index}
			<div class="card p-4">
				<div class="grid max-w-3xl grid-cols-1 gap-8">
					<div class="card variant-filled-primary grid grid-cols-5 p-4">
						<div class="left-0">{index + 1}.</div>

						<IconUserCircle class="-ml-7"></IconUserCircle>

						<div class="col-span-2 -ml-10">{player.name}</div>
						<div class="-ml-5">{player.goals} goals</div>
					</div>

					{#if edit}
						<button
							type="button"
							class="variant-filled-primary btn"
							on:click={() => addGoal(player)}><IconPlus></IconPlus></button
						>

						<button
							type="button"
							class="variant-filled-primary btn"
							on:click={() => removeGoal(player)}><IconMinus></IconMinus></button
						>

						<button
							type="button"
							class="variant-filled-primary btn"
							on:click={() => deletePlayer(player)}><IconTrashFilled /></button
						>
					{/if}
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
