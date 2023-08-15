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
		//TODO you can't speedrun if there's a setTimeout delay
		setTimeout(() => (selected = []), 500);
	};

	const startGameTimer = () => {
		const countdown = () => {
			time -= 1;
		};
		timerId = setInterval(countdown, 1000);
	};

	const resetGame = () => {
		timerId && clearInterval(timerId);
		grid = createGrid();
		maxMatches = grid.length / 2;
		selected = [];
		matches = [];
		timerId = null;
		time = 20;
	};

	const gameWon = () => {
		state = 'won';
		resetGame();
	};

	const gameLost = () => {
		state = 'lost';
		resetGame();
	};

	let state: State = 'start';
	let size = 20;
	let grid = createGrid();
	let maxMatches = grid.length / 2;
	let selected: number[] = [];
	let matches: string[] = [];
	let timerId: number | null | undefined | NodeJS.Timeout = null;
	let time: number = 20;

	$: if (state == 'playing') {
		!timerId && startGameTimer();
	}

	$: selected.length == 2 && matchCards();
	$: maxMatches == matches.length && gameWon();
	$: time == 0 && gameLost();

	$: console.log(state, selected, matches);
</script>

<div class="container h-full mx-auto flex justify-center items-center text-center">
	<div class="space-y-5 grid">
		{#if state == 'start'}
			<h1 class="h1">MeMatch Game</h1>
			<button class="btn btn-lg variant-form-material" on:click={() => (state = 'playing')}
				>Play</button
			>
		{/if}

		{#if state == 'playing'}
			<h1
				class="h1"
				class:animate-jump={time <= 10}
				class:animate-infinite={time <= 10}
				class:animate-duration-1000={time <= 10}
				class:animate-ease-in-out={time <= 10}
			>
				{time}
			</h1>

			<div class="flex space-x-4">
				{#each matches as card}
					<div>
						<h2 class="h1">{card}</h2>
					</div>
				{/each}
			</div>

			<section class="grid grid-cols-5 gap-4">
				{#each grid as card, cardIndex}
					<!-- local const's -->
					{@const isSelected = selected.includes(cardIndex)}
					{@const isSelectedOrMatch = selected.includes(cardIndex) || matches.includes(card)}
					{@const match = matches.includes(card)}

					<button
						class="card p-12 card-hover text-7xl"
						class:variant-glass-error={isSelected}
						class:variant-glass-primary={match}
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
	.variant-glass-error {
		transition: opacity 0.5s ease-in-out;
		opacity: 0.7;
	}
	.match {
		transition: opacity 1.5s ease-out;
		opacity: 0.3;
	}
	.variant-glass-primary {
		transition: opacity 1.5 ease-in;
		opacity: 0.8;
	}
</style>
