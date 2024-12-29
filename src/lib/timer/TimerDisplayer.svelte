<script lang="ts">
	interface Props {
		value: number;
		countdownFrom?: number;
		timeString?: string;
		showMilliseconds?: boolean;
	}

	let { value, countdownFrom, showMilliseconds, timeString = $bindable() }: Props = $props();
	let hours = $derived(Math.floor(((countdownFrom ?? 0) - value) / 1000 / 60 / 60));
	let minutes = $derived(Math.floor((((countdownFrom ?? 0) - value) / 1000 / 60) % 60));
	let seconds = $derived(Math.floor((((countdownFrom ?? 0) - value) / 1000) % 60));
	let milliseconds = $derived(((countdownFrom ?? 0) - value) % 1000);

	$effect(() => {
		timeString = `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds
			.toString()
			.padStart(2, '0')}${showMilliseconds ? `.${milliseconds.toString().padStart(3, '0')}` : ''}`;
	});
</script>

<div class="bg-black p-10 text-center font-mono text-8xl font-black">
	{hours.toString().padStart(2, '0')}:{minutes.toString().padStart(2, '0')}:{seconds
		.toString()
		.padStart(2, '0')}{showMilliseconds ? `.${milliseconds.toString().padStart(3, '0')}` : ''}
</div>
