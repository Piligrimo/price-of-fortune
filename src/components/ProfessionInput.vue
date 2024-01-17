<template>
	<modal :visible="visible">
		<select v-model="buffer">
			<option value="CAPTAIN">Капитан</option>
			<option value="NAVIGATOR">Навигатор</option>
			<option value="JUNGE">Юнга</option>
			<option value="COOK">Кок</option>
			<option value="SAILOR">Матрос</option>
			<option value="MEDIUM">Медиум</option>
		</select>
		<button @click="$emit('save', buffer)">Сохранить</button>
		<button @click="$emit('close')">Отмена</button>
	</modal>
</template>
	
<script>
	import Modal from './Modal.vue';
	export default {
		name: 'ProfessionInput',
		components: { Modal },
		props: { 
			visible: Boolean,
			profession: String,
		},
		data() {
			return {
				buffer: this.profession
			}
		},
		watch: {
			profession(val) {
				this.buffer = val;
			}
		}
	}
</script>
	
	<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
	