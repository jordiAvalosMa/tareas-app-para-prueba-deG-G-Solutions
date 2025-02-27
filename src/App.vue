<template>
  <div id="app">
    <TareaForm @tarea-agregada="addTarea" />
    <TareasList ref="tareasList" />
  </div>
</template>

<script>
import TareaForm from './components/TareaForm.vue';
import TareasList from './components/TareasList.vue';

export default {
  components: {
    TareaForm,
    TareasList,
  },
  methods: {
    addTarea() {
      this.$refs.tareasList.fetchTareas(0); 
    },
  },
};
</script>
