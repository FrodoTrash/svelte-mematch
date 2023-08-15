<script lang="ts">
	import { emojis } from '$lib/utils/emojis';
	type State = 'start' | 'playing' | 'won' | 'lost';

	const createGrid = () => {
		let cards = new Set<string>();
		let maxSize = size / 2;

		while (cards.size < maxSize) {
			const randomIndex = Math.floor(Math.random() * emojis.length);
			cards.add(emojis[randomIndex]);
		}

		return shuffle([...cards, ...cards]);
	};

	const shuffle = <Items>(array: Items[]): Items[] => {
		return array.sort(() => Math.random() - 0.5);
	};

	const selectCard = (cardIndex: number) => {
		selected = selected.concat(cardIndex);
	};

	const matchCards = () => {
		const [first, second] = selected;

		if (grid[first] == grid[second]) {
			matches = matches.concat(grid[first]);
		}
		setTimeout(() => (selected = []), 500);
	};

	const gameWon = () => {
		state = 'won';
	};

	let state: State = 'start';
	let size = 20;
	let grid = createGrid();
	let maxMatches = grid.length / 2;
	let selected: number[] = [];
	let matches: string[] = [];

	$: selected.length == 2 && matchCards();
	$: maxMatches == matches.length && gameWon();

	$: console.log(state, selected, matches);
</script>

<div class="container h-full mx-auto flex justify-center items-center">
	<div class="space-y-5 grid">
		{#if state == 'start'}
			<h1 class="h1">MeMatch Game</h1>
			<button class="btn btn-lg variant-form-material" on:click={() => (state = 'playing')}
				>Play</button
			>
		{/if}
		<!-- h-auto max-w-full rounded-lg -->
		{#if state == 'playing'}
			<section class="grid grid-cols-5 gap-4">
				{#each grid as card, cardIndex}
					<!-- local const's -->
					{@const isSelected = selected.includes(cardIndex)}
					{@const isSelectedOrMatch = selected.includes(cardIndex) || matches.includes(card)}
					{@const match = matches.includes(card)}

					<button
						class="card p-12 card-hover text-7xl"
						class:selected={isSelected}
						disabled={isSelectedOrMatch}
						on:click={() => selectCard(cardIndex)}
					>
						<div class:match>{card}</div>
					</button>
				{/each}
			</section>
		{/if}

		{#if state == 'lost'}
			<h1 class="h1">You lost!</h1>
			<button class="btn variant-outline-primary" on:click={() => (state = 'playing')}
				>Play again!</button
			>
		{/if}

		{#if state == 'won'}
			<h1 class="h1">You won!</h1>
			<button class="btn variant-outline-primary" on:click={() => (state = 'playing')}
				>Play again!</button
			>
		{/if}
	</div>
</div>

<style>
	.selected {
		border: 2px solid gold;
	}
	.match {
		transition: opacity 1.5s ease-out;
		opacity: 0.4;
	}
</style>
