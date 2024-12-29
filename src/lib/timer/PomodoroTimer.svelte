<script lang="ts">
	import CountdownTimer from './CountdownTimer.svelte';
	import { onMount } from 'svelte';

	let audio: HTMLAudioElement;

	const smallBreak = $state({ duration: 5 * 60 * 1000, name: 'Break' });
	const longBreak = $state({ duration: 15 * 60 * 1000, name: 'Long Break' });
	const work = $state({ duration: 30 * 60 * 1000, name: 'Work' });

	let pomodorosBeforeLongBreak = $state(4);
	const cycle = $derived.by(() => {
		let a = [];
		for (let i = 1; i <= pomodorosBeforeLongBreak; i++) {
			a.push(work);
			if (i !== pomodorosBeforeLongBreak) a.push(smallBreak);
		}
		a.push(longBreak);
		return a;
	});

	let autostart = $state(false);
	let index = $state(0);
	let startDate = $state(0);
	let startTimer: () => void = $state(() => {});

	onMount(() => {
		audio = new Audio('/ding.mp3');
	});

	let timeString = $state('');
	$effect(() => {
		document.title = timeString.substring(0, 8);
	});
</script>

<div class="flex flex-col">
	<div
		class="p-2 text-center text-4xl {cycle[index % cycle.length].name === 'Work'
			? 'bg-red-700'
			: 'bg-blue-500'}"
	>
		{cycle[index % cycle.length].name}
	</div>
	<CountdownTimer
		bind:timeString
		bind:startTimer
		bind:startDate
		duration={cycle[index % cycle.length].duration}
		onEnd={() => {
			audio.play();
			index += 1;
			if (autostart)
				setTimeout(() => {
					startTimer();
				});
		}}
	/>
	<div class="p-2 text-center text-2xl">
		<label for="date">Start Date:</label>
		<input
			type="datetime-local"
			id="date"
			bind:value={() =>
				new Date(startDate - new Date().getTimezoneOffset() * 60 * 1000)
					.toISOString()
					.substring(0, 23),
			(v) => {
				startDate = new Date(v).getTime();
				// index = Math.max(0, v);
			}}
			class="border-b-2 bg-transparent p-2 text-center text-2xl"
		/>
	</div>
	<div class="p-2 text-center text-2xl">
		<label for="index">Cycle:</label>
		<input
			type="number"
			id="index"
			bind:value={() => index,
			(v) => {
				index = Math.max(0, v);
			}}
			min="0"
			class="border-b-2 bg-transparent p-2 text-center text-2xl"
		/>
	</div>
	<div class="p-2 text-center text-2xl">
		<label for="cycles">Cycles before long break:</label>
		<input
			type="number"
			id="cycles"
			bind:value={() => pomodorosBeforeLongBreak,
			(v) => {
				pomodorosBeforeLongBreak = Math.max(0, v);
			}}
			min="0"
			class="border-b-2 bg-transparent p-2 text-center text-2xl"
		/>
	</div>
	<div class="p-2 text-center text-2xl">
		<label for="autostart">Autostart:</label>
		<input
			type="checkbox"
			id="autostart"
			bind:checked={autostart}
			class="border-b-2 bg-transparent p-2 text-center text-2xl"
		/>
	</div>
	{@render TimeEdit(work)}
	{@render TimeEdit(smallBreak)}
	{@render TimeEdit(longBreak)}
</div>
{#snippet TimeEdit(time: typeof work)}
	<div class="p-2 text-center text-2xl">
		<span>{time.name} duration:</span>
		<input
			type="number"
			bind:value={() => Math.floor(time.duration / 1000 / 60 / 60),
			(v) => {
				time.duration -= (Math.floor(time.duration / 1000 / 60 / 60) % 60) * 1000 * 60 * 60;
				time.duration += Math.max(0, v) * 1000 * 60 * 60;
			}}
			min="0"
			max="60"
			class="border-b-2 bg-transparent p-2 text-center text-2xl"
		/>
		<input
			type="number"
			bind:value={() => Math.floor(time.duration / 1000 / 60) % 60,
			(v) => {
				time.duration -= (Math.floor(time.duration / 1000 / 60) % 60) * 1000 * 60;
				time.duration += Math.max(0, v) * 1000 * 60;
			}}
			min="0"
			max="60"
			class="border-b-2 bg-transparent p-2 text-center text-2xl"
		/>
		<input
			type="number"
			bind:value={() => (time.duration / 1000) % 60,
			(v) => {
				time.duration -= (Math.floor(time.duration / 1000) % 60) * 1000;
				time.duration += Math.max(0, v) * 1000;
			}}
			min="0"
			max="60"
			class="border-b-2 bg-transparent p-2 text-center text-2xl"
		/>
	</div>
{/snippet}
