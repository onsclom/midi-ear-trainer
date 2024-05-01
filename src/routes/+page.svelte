<script lang="ts">
	type Settings = {
		allowedIntervals: number[];
		delayAfterAnswer: number;
		moveOnIfIncorrect: boolean;
		noteRange: [number, number];
	};

	type AppState =
		| {
				state: 'stopped';
		  }
		| {
				state: 'firstNote';
				note: number;
		  }
		| {
				state: 'guessingNote';
				note: number;
		  }
		| {
				state: 'revealedNote';
				note: number;
		  };
	let appState: AppState = $state({ state: 'stopped' });
	const running = $derived(appState.state !== 'stopped');

	const possibleIntervals = [
		'unison',
		'minor second',
		'major second',
		'minor third',
		'major third',
		'perfect fourth',
		'tritone',
		'perfect fifth',
		'minor sixth',
		'major sixth',
		'minor seventh',
		'major seventh',
		'octave',
		'minor ninth',
		'major ninth'
	];
	let allowedIntervals = $state(possibleIntervals.slice(1, 7));

	let midiDeviceConnected = $state(false);
</script>

<main class="m-auto max-w-3xl p-4">
	<p>
		🎹
		{#if midiDeviceConnected}
			<span class="text-green-500">CONNECTED</span>
		{:else}
			<span class="text-red-500">NOT CONNECTED</span>
		{/if}
	</p>

	<button
		class={`w-full border-2 border-black p-4 ${running ? 'bg-red-200' : 'bg-green-200'}`}
		onclick={() => (appState = running ? { state: 'stopped' } : { state: 'firstNote', note: 60 })}
	>
		{running ? 'STOP' : 'START'}
	</button>

	{#if !running}
		<p>Allowed Intervals</p>
		<ul class="flex flex-wrap items-center justify-center">
			{#each possibleIntervals as interval}
				<label
					class={`transition-color m-1 cursor-pointer select-none border border-black p-3 transition-colors ${allowedIntervals.includes(interval) ? 'bg-green-100' : 'bg-gray-200'}`}
					>{interval}
					<input type="checkbox" bind:group={allowedIntervals} value={interval} /></label
				>
			{/each}
		</ul>
		<button
			class="m-auto block border-2 border-black bg-gray-200 p-3"
			onclick={() => {
				allowedIntervals = allowedIntervals.length > 0 ? [] : possibleIntervals.slice();
			}}
		>
			{allowedIntervals.length > 0 ? 'Clear All' : 'Select All'}
		</button>

		<!-- <label
			>Delay after answer
			<input type="range" min="0" max="10000" /> <span>500ms</span>
		</label> -->
	{:else}
		<p class="text-center text-8xl">?</p>
		<!-- <p>Score {correct}/{correct + incorrect}</p> -->
		{running}
	{/if}
</main>
