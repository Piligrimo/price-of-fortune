<template>
  <div v-if="visible" class="modal">
      <div class="modal__card">
          <slot> Модалочка </slot>
      </div>
  </div>
</template>
  
<script>
  export default {
    name: 'MyModal',
    props: { 
      visible: Boolean
    },
  }
</script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .modal {
    background-color: rgba(0, 0, 0, 0.7) ;
    height: 100vh;
    width: 100vw;
    position: fixed;
    top: 0;
    left: 0;
  }
  .modal__card {
    background-color: white;
    border-radius: 5px;
    padding: 20px;
    margin: 70px auto;
    max-width: 400px;
  }

@media (max-width: 600px) {
  .modal__card {
    max-width: 200px;
  }
}
</style>
  