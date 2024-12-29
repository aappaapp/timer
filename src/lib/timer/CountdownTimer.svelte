<script lang="ts">
	interface Props {
		duration: number;
		running?: boolean;
		startDate?: number;
		timeString?: string;
		/** Bind this and call it to start the timer */
		startTimer?: () => void;
		onEnd?: () => void;
	}

	import Timer from './Timer.svelte';

	let {
		duration,
		running = $bindable(),
		startDate = $bindable(),
		timeString = $bindable(),
		startTimer = $bindable(),
		onEnd
	}: Props = $props();
</script>

<Timer
	countdownFrom={duration}
	{onEnd}
	bind:running
	bind:startDate
	bind:timeString
	bind:startTimer
/>
