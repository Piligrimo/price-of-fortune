<template>
	<modal :visible="visible">
		<div class="coin-container">
			<div class="coin" v-for="(coin, i) in coins" :key="i">
				<span v-if="coin">1</span>
			</div>
		</div>
		<h1>={{ sum }}</h1>
		<button @click="$emit('close')">Готово</button>
	</modal>
</template>
	
<script>
import Modal from './Modal.vue';
	export default {
		name: 'RollingModal',
		components: { Modal },
		props: { 
			visible: Boolean,
			quantity: Number,
		},
		data() {
			return {
				coins:[],
				sum: 0,
			}
		},
		watch: {
			visible(val) {
				if (val) {
					this.coins = []
					this.sum = 0
					for (let i = 0; i < this.quantity; i++) {
						const flip = Math.random() > 0.5 ? 1 : 0
						this.coins.push(flip)
						this.sum +=flip
					}
				}
			}
		}
	}

</script>
	
	<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.coin-container {
	display: flex;
	justify-content: center;
	margin-bottom: 20px;
	flex-wrap: wrap
}

.coin {
	width: 30px;
	height: 30px;
	margin: 5px;
	padding: 5px;
	border-radius: 50%;
	font-size: 24px;
	border: 1px solid black;
	background-color: gold;
}
</style>
	