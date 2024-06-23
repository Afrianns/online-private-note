<script setup lang="ts">
import { defineComponent } from 'vue';

let text = defineProps(['empty']);

defineComponent({
	name: 'MdiFiles',
});
</script>
<template>
	<div class="empty">
		<svg xmlns="http://www.w3.org/2000/svg" width="5em" height="5em" viewBox="0 0 24 24">
			<path fill="currentColor"
				d="M15 7h5.5L15 1.5zM8 0h8l6 6v12a2 2 0 0 1-2 2H8a2 2 0 0 1-2-2V2a2 2 0 0 1 2-2M4 4v18h16v2H4a2 2 0 0 1-2-2V4z" />
		</svg>
		<p>{{ text.empty }}</p>
	</div>
</template>

<style>
.empty {
	color: rgb(150, 150, 150);
	text-align: center;
	margin: 2rem;
	gap: 1rem;
	display: flex;
	flex-direction: column;
	align-items: center;
}

.empty p {
	font-size: .75rem;
	display: inline-block;
}
</style>