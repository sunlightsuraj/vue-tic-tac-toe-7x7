<script setup>
import { computed, ref } from '@vue/reactivity';
import { reactive } from 'vue';
import GameTile from './GameTile.vue';

const props = defineProps({
	n: Number,
	winCount: Number
})
const { n, winCount } = props;
const activePlayer = ref(1);
const finished = ref(false);
const winner = ref(0);
var matrix = reactive(Array(n).fill(Array(n).fill(0)));

const checkVerticalGameWin = (r, c) => {
	let continueousCount = 1;

	// check vertical up
	for (let i = (r - 1);i >= 0;i--) {
		console.log(`vu: ${i}:${c} => ${matrix[i][c]}`)
		if ((matrix[i][c] !== activePlayer.value) || continueousCount >= winCount) {
			break;
		}

		continueousCount++;
	}
	if (continueousCount < winCount) {
		// check vertical down
		for (let i = (r + 1);i < n;i++) {
			console.log(`vd: ${i}:${c} => ${matrix[i][c]}`)
			if ((matrix[i][c] !== activePlayer.value) || continueousCount >= winCount) {
				break;
			}
			continueousCount++;
		}
	}

	return continueousCount;
}

const checkHorizontalGameWin = (r, c) => {
	let continueousCount = 1;

	// check horizontal up
	for (let i = (c - 1);i >= 0;i--) {
		if ((matrix[r][i] !== activePlayer.value) || continueousCount >= winCount) {
			break;
		}
		continueousCount++;
	}
	if (continueousCount < winCount) {
		// check horizontal down
		for (let i = (c + 1);i < n;i++) {
			if ((matrix[r][i] !== activePlayer.value) || continueousCount >= winCount) {
				break;
			}
			continueousCount++;
		}
	}

	return continueousCount;
}

const checkLeftCrossGameWin = (r, c) => {
	let continueousCount = 1;

	// check left cross up
	let i = 1;
	while ((r - i >= 0) && (c - i >= 0)) {
		if ((matrix[r - i][c - i] !== activePlayer.value) || continueousCount >= winCount) {
			break;
		}
		continueousCount++;
		i++;
	}
	if (continueousCount < winCount) {
		// check right cross down
		i = 1;
		while ((r + i < n) && (c + i < n)) {
			if ((matrix[r + i][c + i] !== activePlayer.value) || continueousCount >= winCount) {
				break;
			}
			continueousCount++;
			i++;
		}
	}

	return continueousCount;
}

const checkRightCrossGameWin = (r, c) => {
	let continueousCount = 1;

	// check right cross up
	let i = 1;
	while ((r - i >= 0) && (c + i < n)) {
		if ((matrix[r - i][c + i] !== activePlayer.value) || continueousCount >= winCount) {
			break;
		}
		continueousCount++;
		i++;
	}
	if (continueousCount < winCount) {
		// check left cross down
		i = 1;
		while ((r + i < n) && (c - i >= 0)) {
			if ((matrix[r + i][c - i] !== activePlayer.value) || continueousCount >= winCount) {
				break;
			}
			continueousCount++;
			i++;
		}
	}

	return continueousCount;
}

const checkGameWin = (r, c) => {

	// check vertical
	let count = checkVerticalGameWin(r, c);
	console.log('v', count);
	if (count < winCount) {
		// check horizontal
		count = checkHorizontalGameWin(r, c);
		console.log('h', count);
		if (count < winCount) {
			// check upper left to right down cross
			count = checkLeftCrossGameWin(r, c);
			console.log('lc', count);
			if (count < winCount) {
				// check upper right to left down cross
				count = checkRightCrossGameWin(r, c);
				console.log('rc', count);
			}
		}
	}

	if (count >= winCount) {
		finished.value = true;
		winner.value = activePlayer.value;
		return true;
	}

	return false;
}

const onClickBox = (position) => {
	const r = position[0];
	const c = position[1];
	console.log('matrix', finished.value, matrix[r][c])
	if (!finished.value && matrix[r][c] === 0) {

		matrix = [
			...matrix.slice(0, r),
			[
				...matrix[r].slice(0, c),
				activePlayer.value,
				...matrix[r].slice(c + 1, matrix[r].length)
			],
			...matrix.slice(r + 1, matrix.length)
		]

		checkGameWin(r, c);

		activePlayer.value = activePlayer.value === 1 ? 2 : (activePlayer.value === 2 ? 3 : 1);
	}
}

const draw = computed(() => {
	let d = true;
	for (let r = 0;r < matrix.length;r++) {
		for (let c = 0;c < matrix[r].length;c++) {
			if (matrix[r][c] === 0) {
				d = false;
				break;
			}
		}
	}

	return d;
})
</script>

<template>
	<div class="d-flex w-50 m-auto">
		<div style="width: 20%">
			<div>
				<p>
					<span v-if="activePlayer === 1" className="m-2">===&gt;</span>
					<span>Player 1: X</span>
				</p>
				<p>
					<span v-if="activePlayer === 2" className="m-2">===&gt;</span>
					<span>Player 2: O</span>
				</p>
				<p>
					<span v-if="activePlayer === 3" className="m-2">===&gt;</span>
					<span>Player 3: ^</span>
				</p>
			</div>
			<div>
				<p class="bg-success p-2 text-while" v-if="winner !== 0">Winner: Player {{ winner }}</p>
			</div>
			<div>
				<p v-if="draw">Game Draw</p>
			</div>
		</div>
		<div class="card w-50 m-auto rounded-0 p-0">
			<div class="card-body p-0">
				<div class="d-flex" v-for="(row, i) of matrix" :key="i">
					<GameTile 
						v-for="(player, j) of row" 
						:key="i + '' + j" 
						:player="player" 
						:position="[i, j]" 
						@tileClick="(position) => onClickBox(position)"
					></GameTile>
				</div>
			</div>
		</div>
	</div>
</template>

<style scoped>

</style>