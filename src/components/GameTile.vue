<script setup>
	const props = defineProps({
		player: Number,
		position: Array
	})

	const emit = defineEmits(['tileClick'])

	const onClick = () => {
		emit('tileClick', props.position);
	}
</script>

<template>
	<div class="box bg-success text-center align-middle m-1 p-1 fw-bold fs-4" @click="onClick">
		{{ player === 1 ? 'X' : (player === 2 ? 'O' : (player === 3 ? '^' : '')) }}
	</div>
</template>

<style scoped>

</style>