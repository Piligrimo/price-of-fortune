<template>
	<modal :visible="visible">
		<h3>{{ skill && skill.name }}</h3>
		<div class="descr">
			{{ skill && skill.description }}
		</div>
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
			skill: Object,
		},
	}

</script>
	
	<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.descr {
	margin-bottom: 20px;
}
</style>
	