<script setup>
import { ref } from 'vue';

import GameBoard from './components/GameBoard.vue';
const session = ref(false);
const n = 7;
const winCount = 6;

const setNewSession = () => {
	session.value = true;
}

</script>

<template>
	<main class="App text-center mt-5">
		<GameBoard v-if="session" :n="n" :winCount="winCount"></GameBoard>
		<button class="btn btn-primary btn-lg rounded-0" v-else @click="setNewSession">New Game</button>
	</main>
</template>
