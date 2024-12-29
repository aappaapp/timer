<script lang="ts">
	interface Props {
		countdownFrom?: number;
		running?: boolean;
		startDate?: number;
		timeString?: string;
		/** Bind this and call it to start the timer */
		startTimer?: () => void;
		onEnd?: () => void;
	}

	import TimerDisplayer from './TimerDisplayer.svelte';

	let {
		countdownFrom,
		running = $bindable(),
		startDate = $bindable(),
		timeString = $bindable(),
		startTimer = $bindable(),
		onEnd
	}: Props = $props();

	let difference: number = $state(0);

	startTimer = () => {
		running = true;
	};

	// Reset timer when countdownFrom changes
	$effect(() => {
		countdownFrom;
		startDate = Date.now();
		difference = 0;
		running = false;
	});

	// Update timer every 10ms
	let interval: number;
	$effect(() => {
		if (running)
			interval = setInterval(() => {
				difference = Date.now() - (startDate ?? 0);
				if (countdownFrom && difference >= countdownFrom) {
					running = false;
					difference = countdownFrom;
					onEnd?.();
				}
			}, 10);
		else clearInterval(interval);
		return () => clearInterval(interval);
	});
</script>

<div class="flex flex-col">
	<TimerDisplayer bind:timeString value={difference} {countdownFrom} showMilliseconds />

	<div class="flex">
		{#if !running}
			{#if difference <= 0}
				<button
					onclick={() => {
						startDate = Date.now();
						running = true;
					}}
					class="flex-1 bg-red-700 p-5 text-center text-4xl font-bold hover:bg-red-600"
					>Start Timer</button
				>
			{:else}
				<button
					onclick={() => {
						startDate = Date.now() - difference;
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
					startDate = Date.now();
					difference = 0;
					running = false;
				}}
				class="flex-1 bg-red-700 p-5 text-center text-4xl font-bold hover:bg-red-600"
				>Reset Timer</button
			>
		{/if}
	</div>
</div>
