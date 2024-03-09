<template>
	<div class="campo">
		<div class="linha" v-for="(linha, index) in campo" :key="index">
			<div
				class="cell"
				v-for="(cell, jndex) in linha"
				:key="jndex"
				:class="{
					bomb: cell.bomb,
					ocult: !cell.open,
					mark: cell.mark,
				}"
				@click="cell.open = true"
				@contextmenu.prevent="cell.mark = !cell.mark"
			>
				{{ cell.value || "" }}
			</div>
		</div>
	</div>
	<div v-if="gameOver" class="gameOver" @click="reload()">Game&nbsp;Over</div>
	<div v-if="endGame" class="gameOver" @click="reload()">Sucesso!</div>
</template>

<script>
	export default {
		name: "app",
		data() {
			return {
				campo: [[]],
			};
		},

		computed: {
			bombas() {
				return this.campo.flat().filter((i) => i.bomb).length;
			},
			livres() {
				return this.campo.flat().filter((i) => i.open && !i.bomb).length;
			},
			explodidas() {
				return this.campo.flat().filter((i) => i.open && i.bomb).length;
			},
			marcadas() {
				return this.campo.flat().filter((i) => i.mark).length;
			},
			gameOver() {
				return !!this.explodidas;
			},
			endGame() {
				return this.campo.flat().filter((i) => i.mark && i.bomb).length == this.bombas;
			},
		},
		methods: {
			reload() {
				location.reload();
			},
			createGrid() {
				let size = 5;
				let p = 0.2;
				this.campo = [...Array(size).keys()].map(() =>
					[...Array(size).keys()].map((cell) => {
						return {
							bomb: Math.random() <= p,
							open: false,
							value: 0,
							mark: false,
						};
					})
				);
				for (let i = 0; i < size; i++) {
					for (let j = 0; j < size; j++) {
						if (!this.campo[i][j].bomb) {
							this.campo[i][j].value = this.countBombs(this.campo, i, j);
						}
					}
				}
			},
			countBombs(a, i, j) {
				let count = 0;
				if (a[i - 1] && a[i - 1][j - 1] && a[i - 1][j - 1].bomb) count = count + 1;
				if (a[i - 1] && a[i - 1][j] && a[i - 1][j].bomb) count = count + 1;
				if (a[i - 1] && a[i - 1][j + 1] && a[i - 1][j + 1].bomb) count = count + 1;

				if (a[i] && a[i][j - 1] && a[i][j - 1].bomb) count = count + 1;
				if (a[i] && a[i][j + 1] && a[i][j + 1].bomb) count = count + 1;

				if (a[i + 1] && a[i + 1][j - 1] && a[i + 1][j - 1].bomb) count = count + 1;
				if (a[i + 1] && a[i + 1][j] && a[i + 1][j].bomb) count = count + 1;
				if (a[i + 1] && a[i + 1][j + 1] && a[i + 1][j + 1].bomb) count = count + 1;
				return count;
			},
		},

		mounted() {
			this.createGrid();
		},
	};
</script>

<style>
	@import url("https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap");
	.campo {
		border: 1px solid white;
	}

	.linha {
		display: flex;
	}
	.cell {
		height: 45px;
		width: 45px;
		border: 1px solid white;
		display: flex;
		align-items: center;
		justify-content: center;
		cursor: pointer;
	}
	.ocult {
		opacity: 0;
	}
	.bomb {
		background: red;
	}
	.mark {
		background: white;
		opacity: 1;
	}
	.gameOver {
		font-family: "Press Start 2P", cursive;
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		color: white;
		font-size: 3rem;
	}
</style>
