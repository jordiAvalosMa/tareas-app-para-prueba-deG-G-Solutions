<template>
  <div class="task-container">
    <div class="header-container">
      <div class="titulo-tareas">
        <i class="fas fa-home"></i> Tareas
      </div>
      <div class="contenedor-boton">
        <button @click="mostrarFormulario" class="btn-agregar-tarea">Agregar Tarea</button>
      </div>
    </div>

    <div v-if="formVisible" class="modal-overlay" @click="cerrarFormulario">
      <div class="modal-content" @click.stop>
        <h2>Agregar Nueva Tarea</h2>
        <form @submit.prevent="addTarea" class="formulario-tarea">
          <div>
            <label for="titulo">Título</label>
            <input type="text" id="titulo" v-model="titulo" required />
          </div>
          <div>
            <label for="descripcion">Descripción</label>
            <textarea id="descripcion" v-model="descripcion"></textarea>
          </div>
          <div class="botones-formulario">
            <button type="submit" class="btn-agregar-tarea">Agregar Tarea</button>
            <button type="button" @click="cerrarFormulario" class="btn-cancelar">Cancelar</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      titulo: '',
      descripcion: '',
      formVisible: false,
    };
  },
  methods: {
    mostrarFormulario() {
      this.formVisible = true;
    },
    cerrarFormulario() {
      this.formVisible = false;
      this.titulo = '';
      this.descripcion = '';
    },
    async addTarea() {
      if (!this.titulo.trim()) {
        alert('El título es obligatorio');
        return;
      }

      try {
        const response = await axios.post('http://localhost:8000/api/tareas', {
          titulo: this.titulo,
          descripcion: this.descripcion,
        });
        this.$emit('tarea-agregada', response.data); 
      } catch (error) {
        console.error("Hubo un error al agregar la tarea:", error);
      }
    },
  },
};
</script>

<style scoped>
.header-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.titulo-tareas {
  font-size: 1.5rem;
  font-weight: bold;
  display: flex;
  align-items: center;
}

.titulo-tareas i {
  margin-right: 8px;
}

.contenedor-boton {
  display: flex;
  justify-content: flex-end; 
}
.btn-agregar-tarea {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 12px 12px;
  font-size: 1rem;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.btn-agregar-tarea:hover {
  background-color: #0056b3;
}
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
}

.modal-content {
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
  width: 400px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

h2 {
  text-align: center;
  margin-bottom: 20px;
  color: #333;
  font-size: 2rem;
}

.formulario-tarea {
  display: flex;
  flex-direction: column;
}
.formulario-tarea input,
.formulario-tarea textarea {
  width: 100%;
  padding: 12px 15px;
  margin: 10px 0;
  border-radius: 5px;
  border: 1px solid #ddd;
  box-sizing: border-box;
  font-size: 1rem;
  color: #333;
}

.botones-formulario {
  display: flex;
  justify-content: space-between;
}

.formulario-tarea button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 12px 12px;
  font-size: 1rem;
  border-radius: 5px;
  cursor: pointer;
  width: 48%; 
}

.formulario-tarea button:hover {
  background-color: #0056b3;
}

textarea {
  resize: vertical;
  height: 100px;
}


.btn-cancelar {
  background-color: #ccc;
  color: white;
  border: none;
  padding: 15px 15px;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 1px;
}
.task-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 10px;
  background-color: #fff;
  border-radius: 4px;
  margin-top: 10px;
}
</style>