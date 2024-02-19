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

<input type="text" placeholder="Add new player" bind:value={name} />
<button on:click={addPlayer}>Add</button>
<br />

<ul>
	{#each players as player}
		<li>
			<span>{player.name} - </span>
			<span>{player.goals} goals</span>
			<span><button on:click={() => addGoal(player)}>+</button></span>
			<span><button on:click={() => removeGoal(player)}>-</button></span>
			<span><button on:click={() => deletePlayer(player)}>Delete</button></span>
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
