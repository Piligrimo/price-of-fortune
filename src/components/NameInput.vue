<template>
	<modal :visible="visible">
		<input v-model="bufferName" />
		<button @click="$emit('save', bufferName)">Сохранить</button>
		<button @click="$emit('close')">Отмена</button>
	</modal>
</template>
	
<script>
import Modal from './Modal.vue';
	export default {
		name: 'NameInput',
		components: { Modal },
		props: { 
			visible: Boolean,
			name: String,
		},
		data() {
			return {
				bufferName: this.name
			}
		},
		watch: {
			name(val) {
				this.bufferName = val;
			}
		}
	}

</script>
	
	<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
	