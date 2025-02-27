<template>
  <div class="task-container">

    <div class="filter-buttons">
      <button @click="fetchTareas()" class="btn btn-todas" aria-label="Mostrar todas las tareas">Todas</button>
      <button @click="fetchTareas(1)" class="btn btn-completadas" aria-label="Mostrar tareas completadas">Completadas</button>
      <button @click="fetchTareas(0)" class="btn btn-pendientes" aria-label="Mostrar tareas pendientes">Pendientes</button>
    </div>

    <div v-if="loading" class="loading" role="status" aria-live="polite">
      <div class="spinner"></div>
    </div>

    <div v-if="error" class="error-message" role="alert">
      <p>{{ errorMessage }}</p>
    </div>

    <div v-if="!loading && !error" class="task-table">
      <table v-if="tareas.length">
        <thead>
          <tr>
            <th>Título</th>
            <th>Descripción</th>
            <th>Estado</th>
            <th>Seleccionar</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="tarea in tareas" :key="tarea.id" :class="{ 'tarea-completada': tarea.completada }">
            <td>{{ tarea.titulo }}</td>
            <td>{{ tarea.descripcion }}</td>
            <td>{{ tarea.completada ? 'Completada' : 'Pendiente' }}</td>
            <td>
              <input type="checkbox" v-model="tarea.completada" @change="toggleEstado(tarea)" aria-label="Cambiar estado de la tarea" />
            </td>
          </tr>
        </tbody>
      </table>

      <div v-else>
        <p>No hay tareas disponibles.</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      tareas: [],
      loading: false,
      error: false,
      errorMessage: '',
    };
  },
  methods: {

    async fetchTareas(completada) {
      this.loading = true;
      this.error = false;

      try {
        const url = completada !== undefined 
          ? `http://localhost:8000/api/tareas/estado/${completada}` 
          : 'http://localhost:8000/api/tareas'; 
        const response = await axios.get(url);

        this.tareas = response.data.data.map(tarea => ({
          ...tarea,
          completada: Boolean(tarea.completada), 
        }));
      } catch (error) {
        this.error = true;
        if (error.response) {
          this.errorMessage = `Error: ${error.response.status} - ${error.response.data.message}`;
        } else {
          this.errorMessage = 'Hubo un error al cargar las tareas. Intenta nuevamente.';
        }
        console.error("Hubo un error al cargar las tareas:", error);
      } finally {
        this.loading = false;
      }
    },

    async toggleEstado(tarea) {
      try {
        const updatedEstado = tarea.completada;
        const response = await axios.put(`http://localhost:8000/api/tareas/${tarea.id}`, {
          completada: updatedEstado,
        });
        if (response.status === 200) {
          console.log("Estado actualizado correctamente.");
        }
      } catch (error) {
        console.error("Hubo un error al cambiar el estado de la tarea:", error);
      }
    },
  },
  created() {
    this.fetchTareas(); 
  },
};
</script>

<style scoped>
body {
  font-family: 'Arial', sans-serif;
  background-color: #f4f6f9;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

h2 {
  text-align: center;
  margin-bottom: 20px;
  color: #333;
  font-size: 2rem;
}


.task-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 10px;
  background-color: #fff;
  border-radius: 4px;
  margin-top: 10px;
}


.filter-buttons {
  display: flex;
  justify-content: flex-end; 
  margin-bottom: 0.15rem; 
}

.filter-buttons button {
  background-color: white;
  color: #007bff;
  border: none; 
  padding: 7px 14px; 
  font-size: 0.9rem; 
  cursor: pointer;
  margin: 0 2px; 
}

.filter-buttons .btn-completadas {
  border-top: 1px solid #007bff;
  border-left: 1px solid #007bff;
  border-right: 1px solid #007bff;
}

.filter-buttons .btn-pendientes {
  border-top: 1px solid #007bff;
  border-left: 1px solid #007bff;
  border-right: 1px solid #007bff;
}

.filter-buttons .btn-todas {
  border-top: 1px solid #007bff;
  border-left: 1px solid #007bff;
  border-right: 1px solid #007bff;
}

.loading {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 200px;
}

.spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-top: 4px solid #007bff;
  border-radius: 50%;
  width: 50px;
  height: 50px;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.error-message {
  text-align: center;
  color: #ff0000;
  font-size: 1.2rem;
  margin-top: 20px;
}

.task-table {
  margin-top: 20px;
  width: 100%;
  overflow-x: auto;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

table {
  width: 100%;
  border-collapse: collapse;
  font-size: 1rem;
  text-align: left;
  border-radius: 8px;
  overflow: hidden;
}

table th, table td {
  padding: 12px 15px;
  border: 1px solid #ddd;
}

table th {
  background-color: #007bff;
  color: white;
  text-transform: uppercase;
}

table td {
  background-color: #fff;
  color: #333;
}

table tr:hover {
  background-color: #f1f1f1;
}
.tarea-completada td {
  text-decoration: line-through;
  color: #999;
  background-color: #f8f9fa;
}

input[type="checkbox"] {
  transform: scale(1.5);
  margin-left: 10px;
  cursor: pointer;
}

div > p {
  text-align: center;
  color: #999;
  font-size: 1.1rem;
}
</style>
