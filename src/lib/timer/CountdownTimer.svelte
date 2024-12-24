<script lang="ts">
	interface Props {
		duration: number;
		onEnd?: () => void;
		startTimer?: () => void;
		string?: string;
	}

	import Timer from './Timer.svelte';

	let { duration, onEnd, startTimer = $bindable(), string = $bindable() }: Props = $props();
</script>

<Timer countdownFrom={duration} {onEnd} bind:startTimer bind:string />
