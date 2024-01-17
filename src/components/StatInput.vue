<template>
	<modal :visible="visible">
		<h1> {{ value }} <span class="bonus" v-if="bonus"> +{{ bonus }}</span></h1>
		<button v-if="value" @click="$emit('save', 0)">Сбросить</button>
		<div v-else class="pool-items">
			<div 
				class="pool-item"
				v-for="(item, i) in pool"  
				:key="i" @click="$emit('save', item)"
			>
				{{ item }} 
			</div>
		</div>
		<button @click="$emit('close')">Отмена</button>
	</modal>
</template>
	
<script>
import { professionsDictionary } from '@/utils/dictionaries';
import Modal from './Modal.vue';
	export default {
		name: 'StatInput',
		components: { Modal },
		props: { 
			visible: Boolean,
			profession: String,
			stat: String,
			value: Number,
			pool: Array
		},
		data() {
			return {
				buffer: this.value
			}
		},
		watch: {
			value(val) {
				this.buffer = val;
			}
		},
		computed: {
			bonus () {
				return professionsDictionary[this.profession]?.bonuses[this.stat] || 0
			}
		}
	}

</script>
	
	<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
	.bonus {
		color: gray;
	}

	.pool-items {
		display: flex;
		flex-direction: row;
		justify-content: center;
		margin-bottom: 40px;
	}

	.pool-item {
		border: 1px solid rgb(39, 39, 39);
		text-align: center;
		margin: 10px;
		width: 20px;
		padding: 5px 10px;
		font-size: 24px;
	}
</style>
	