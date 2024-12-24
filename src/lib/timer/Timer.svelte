<script lang="ts">
	interface Props {
		running?: boolean;
		countdownFrom?: number;
		onEnd?: () => void;
		startTimer?: () => void;
		string?: string;
	}

	import TimerDisplayer from './TimerDisplayer.svelte';

	let {
		running = $bindable(),
		countdownFrom,
		onEnd,
		startTimer = $bindable(),
		string = $bindable()
	}: Props = $props();

	let start: number = $state(0);
	let difference: number = $state(0);

	startTimer = () => {
		running = true;
	};

	$effect(() => {
		countdownFrom;
		start = Date.now();
		difference = 0;
		running = false;
	});
	let interval: number;
	$effect(() => {
		if (running)
			interval = setInterval(() => {
				difference = Date.now() - start;
				if (countdownFrom && difference >= countdownFrom) {
					running = false;
					difference = countdownFrom;
					onEnd?.();
				}
			}, 10);
		else clearInterval(interval);
	});
</script>

<div class="flex flex-col">
	<TimerDisplayer bind:string value={difference} {countdownFrom} showMilliseconds />

	<div class="flex">
		{#if !running}
			{#if difference <= 0}
				<button
					onclick={() => {
						start = Date.now();
						running = true;
					}}
					class="flex-1 bg-red-700 p-5 text-center text-4xl font-bold hover:bg-red-600"
					>Start Timer</button
				>
			{:else}
				<button
					onclick={() => {
						start = Date.now() - difference;
						running = true;
					}}
					class="flex-1 bg-red-700 p-5 text-center text-4xl font-bold hover:bg-red-600"
					>Resume Timer</button
				>
			{/if}
		{:else}
			<button
				onclick={() => {
					running = false;
				}}
				class="flex-1 bg-red-700 p-5 text-center text-4xl font-bold hover:bg-red-600"
				>Pause Timer</button
			>
		{/if}
		{#if difference > 0}
			<button
				onclick={() => {
					start = Date.now();
					difference = 0;
					running = false;
				}}
				class="flex-1 bg-red-700 p-5 text-center text-4xl font-bold hover:bg-red-600"
				>Reset Timer</button
			>
		{/if}
	</div>
</div>
